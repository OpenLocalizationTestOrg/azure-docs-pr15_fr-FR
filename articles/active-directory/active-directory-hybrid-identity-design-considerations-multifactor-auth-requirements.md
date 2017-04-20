<properties
    pageTitle="Azure Active Directory hybride identité conception - déterminer la configuration requise pour l’authentification multifacteur"
    description="Contrôle d’accès conditionnelle, Azure Active Directory vérifie les conditions spécifiques que vous avez choisi lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application."
    documentationCenter=""
    services="active-directory"
    authors="femila"
    manager="billmath"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Déterminer les besoins de l’authentification multifacteur pour votre solution identité hybride

Dans ce monde de la mobilité, avec des utilisateurs l’accès aux données et applications dans le cloud et à partir de n’importe quel appareil, la sécurisation de ces informations est devenu essentiel.  Chaque jour est un nouveau titre sur la violation de la sécurité.  Bien qu’il n’existe aucune garantie par rapport à ces violations, l’authentification multifacteur, fournit un niveau supplémentaire de sécurité afin d’éviter ces violations des règles.
Commencez par évaluer les besoins d’organisations de l’authentification multifacteur. Autrement dit, que l’organisation tente de sécuriser.  Cette évaluation est importante de définir la configuration technique requise pour installer et activer les utilisateurs d’organisations de l’authentification multifacteur.

>[AZURE.NOTE]
Si vous n’êtes pas familiarisé avec l’authentification Multifacteur et qu’il fait, il est recommandé de lire l’article [Quel est l’authentification multifacteur Azure ?](../multi-factor-authentication/multi-factor-authentication.md) avant de poursuivre la lecture de cette section.

Veillez à répondre à ce qui suit :

- Votre entreprise tente de sécuriser Microsoft applications ? 
- Comment ces applications sont publiées ?
- Votre entreprise fournit un accès à distance pour permettre aux employés accéder aux applications en local ?

Si Oui, quel type d’accès à distance ? Vous devez également évaluer dans lequel les utilisateurs qui accèdent à ces applications seront situés. Cette évaluation est une autre étape importante pour définir la stratégie d’authentification multifacteur appropriée. Assurez-vous de répondre aux questions suivantes :

- Où sont les utilisateurs agir en trouve ?
- Pouvant ils se trouver n’importe où ?
- Votre entreprise veut imposer des restrictions en fonction de l’emplacement de l’utilisateur ?

Après avoir évalué ces exigences, il est important d’également évaluer les besoins de l’utilisateur pour l’authentification multifacteur. Cette évaluation est importante car il définira la configuration requise pour le déploiement de l’authentification multifacteur. Assurez-vous de répondre aux questions suivantes :

- Les utilisateurs sont familiarisé avec l’authentification multifacteur ?
- Certaines utilisations seront requises pour fournir une authentification supplémentaire ?  
 - Si Oui, tout le temps, lorsque provenant des réseaux externes ou l’accès à des applications spécifiques, ou dans d’autres conditions ?
- Les utilisateurs devra-t-il formation sur la configuration et implémenter l’authentification multifacteur ?
- Quels sont les scénarios clés que votre société souhaite activer l’authentification multifacteur pour leurs utilisateurs ?

Après avoir répondu à ces questions, vous ne pourrez pas comprendre s’il existe déjà mis en œuvre l’authentification multifacteur locaux. Cette évaluation est importante de définir la configuration technique requise pour installer et activer les utilisateurs d’organisations de l’authentification multifacteur. Assurez-vous de répondre aux questions suivantes :

- Votre société a besoin pour protéger des comptes dotés de privilèges avec l’authentification Multifacteur ?
- Votre société a besoin pour activer l’authentification Multifacteur pour certaines application pour des raisons de conformité ?
- Votre société a besoin pour activer l’authentification Multifacteur pour tous les utilisateurs éligibles de ces applications ou seuls les administrateurs ?
- Vous devez dois-je l’authentification Multifacteur toujours activé ou uniquement lorsque les utilisateurs sont connectés en dehors de votre réseau d’entreprise ?


## <a name="next-steps"></a>Étapes suivantes
[Définir une stratégie d’adoption identité hybride](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)


## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
