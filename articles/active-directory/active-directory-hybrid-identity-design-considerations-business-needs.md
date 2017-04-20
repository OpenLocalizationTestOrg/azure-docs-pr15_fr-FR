<properties
    pageTitle="Azure Active Directory hybride identité conception - déterminer les besoins d’identité | Microsoft Azure"
    description="Identifier les besoins professionnels de la société qui vous permet de définir la configuration requise pour la conception d’identité hybride ouvrira."
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

# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Déterminer les besoins d’identité pour votre solution identité hybride
La première étape de la conception d’une solution d’identité hybride consiste à déterminer la configuration requise pour l’organisation d’entreprise qui va exploiter cette solution.  Identité hybride démarre en tant que lorsqu’il est combiné (qui prend en charge tous les autres solutions de cloud grâce à l’authentification) et accède à fournir des fonctionnalités nouvelles et intéressantes que déverrouiller nouvelles charges de travail pour les utilisateurs.  Ces charges de travail ou les services que vous souhaitez adopter pour vos utilisateurs détermine la configuration requise pour la conception d’identité hybride.  Vous avez besoin de ces services et les charges de travail exploiter identité hybride à la fois en local et dans le cloud.  

Vous devez passer en revue ces aspects essentiels de l’entreprise à comprendre qu’il est maintenant une demande et l’entreprise envisage à l’avenir. Si vous n’avez pas la visibilité de la stratégie à long terme pour la conception d’identité hybride, sans doute que votre solution ne sera pas scalable selon les besoins de l’entreprise agrandir et modifier.   T diagramme ci-dessous montre un exemple d’une architecture d’identité hybride et les charges de travail qui sont en cours déverrouillées pour les utilisateurs. Il s’agit qu’un exemple de toutes les nouvelles possibilités qui peuvent être déverrouillées et remis avec une stratégie d’identité hybride Uni. 
 
Certains composants qui font partie de l’architecture d’identité hybride![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Déterminer les besoins professionnels
Chaque entreprise a des exigences différentes, même si ces sociétés font partie de la même secteur d’activité, l’entreprise réel exigences peuvent varier. Vous pouvez toujours tirer parti de meilleures pratiques à partir du secteur, mais il est finalement les besoins professionnels de la société qui vous permet de définir la configuration requise pour la conception d’identité hybride ouvrira. 

Assurez-vous de répondre aux questions suivantes pour identifier les besoins de votre entreprise :

- Votre entreprise cherche pour découper des coûts d’exploitation informatique ?
- Votre entreprise cherche pour sécuriser les ressources de cloud (applications SaaS, infrastructure) ?
- Votre entreprise cherche à moderniser votre service informatique ?
  - Sont vos utilisateurs plus mobile et exigeant IT et créer des exceptions dans votre DMZ pour permettre à un type différent du trafic pour accéder aux différentes ressources ?
  - Votre entreprise a-t-elle applications héritées nécessaire à publier à ces utilisateurs modernes mais qui ne sont pas faciles à la réécriture de ?
  - Votre société a besoin pour accomplir toutes ces tâches et mettez-le sous contrôle en même temps ?
- Votre entreprise cherche pour sécuriser identité des utilisateurs et réduire les risques en mettant des nouveaux outils qui s’appuient sur l’expérience de Microsoft Azure sécurité expertise en local ?
- Votre entreprise tente de se débarrasser des comptes redoutées « externes » en local et les déplacer vers le cloud où ils ne sont plus une menace en sommeil au sein de votre environnement local ?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analyser l’infrastructure d’identité local
À présent que vous avez une idée concernant vos besoins d’entreprise, vous devez évaluer votre infrastructure d’identité local. Cette évaluation est importante pour définir les exigences techniques pour intégrer votre solution identité actuelle du système de gestion des identités de cloud. Assurez-vous de répondre aux questions suivantes :

- Quelle solution d’authentification et d’autorisation signifie votre société utilisez localement ? 
- Votre entreprise a-t-elle actuellement des services de synchronisation local ?
- Votre entreprise utilise les fournisseurs d’identité tiers (IdP) ?

Vous devez également tenir compte des services cloud que votre société peut avoir. Il est très important d’effectuer une évaluation pour mieux comprendre l’intégration avec les modèles de SaaS, IaaS ou PaaS dans votre environnement en cours. Assurez-vous de répondre aux questions suivantes au cours de cette évaluation :
- Votre entreprise a-t-elle l’intégration à un fournisseur de services de cloud ?
- Si Oui, quels services sont utilisés ?
- Cette intégration n’est actuellement en production ou s’agit-il d’une phase pilote ?


>[AZURE.NOTE]
Si vous n’avez un mappage précis de vos applications et services en nuage, vous pouvez utiliser l’outil de découverte d’application Cloud. Cet outil peut fournir le visibilité entreprise de l’ensemble de l’entreprise et les applications de cloud consommateur de votre service informatique. Qui facilite plus que jamais à découvrir ombre informatique de votre organisation, y compris les détails sur les modèles d’utilisation et tous les utilisateurs l’accès à vos applications cloud. Pour accéder à cet outil, accédez à [https://appdiscovery.azure.com](https://appdiscovery.azure.com/)

## <a name="evaluate-identity-integration-requirements"></a>Évaluer les besoins de l’intégration d’identité
Ensuite, vous devez évaluer les exigences de l’intégration d’identité. Cette évaluation est importante de définir les impératifs techniques de comment les utilisateurs va authentifier, quoi de présence de l’organisation dans le cloud, comment l’organisation permettra d’autorisation et que l’expérience utilisateur va agir en. Assurez-vous de répondre aux questions suivantes :

- Votre organisation utilisent la fédération, l’authentification standard ou les deux ?
- Fédération n’est une obligation ?  En raison de ce qui suit :
 - Authentification basée sur Kerberos
 - Votre entreprise utilise des applications locaux (soit intégré en interne ou 3e partie) qui utilise SAML ou des fonctionnalités de fédération similaires.
 - Authentification Multifacteur via des cartes à puce. RSA SecurID, etc.
 - Règles d’accès client qui permettent de traiter les questions ci-dessous :
     1. Puis-je empêcher tout accès externe à Office 365 en fonction de l’adresse IP du client ?
     1. Puis-je empêcher tout accès externe à Office 365, à l’exception d’Exchange ActiveSync ?
     1. Puis-je empêcher tout accès externe à Office 365, à l’exception des applications basés sur navigateur (OWA, SPO)
     1. Puis-je empêcher tout accès externe à Office 365 pour les membres de groupes Active Directory désignés
- Problèmes de sécurité / d’audit
- Investissement déjà existant dans l’authentification fédérée
- Quel nom de l’organisation utiliseront pour notre domaine dans le cloud ?
- L’organisation possède un domaine personnalisé ?
    1. Est ce domaine public et facilement vérifiables via DNS ?
    1. Si elle n’est pas le cas, puis vous avez un domaine public qui peut être utilisé pour enregistrer une autre UPN dans Active Directory ?
- Sont les identificateurs utilisateur cohérents dans représentation cloud ? 
- L’organisation possède des applications qui nécessitent l’intégration avec les services cloud ?
- L’organisation possède plusieurs domaines et ils sont tous utilisera l’authentification standard ou fédérée ?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Évaluer les applications qui s’exécutent dans votre environnement
À présent que vous avez une idée en ce qui concerne vos localement et infrastructure le cloud, vous devez évaluer les applications qui s’exécutent dans ces environnements. Cette évaluation est importante de définir les exigences techniques à intégrer ces applications pour le système de gestion des identités de cloud. Assurez-vous de répondre aux questions suivantes :

- Où résident nos applications ?
- Les utilisateurs accédant à applications en local ?  Dans le cloud ? Ou les deux ?
- Existe-t-il des plans pour prendre les charges de travail d’application existant et les déplacer vers le cloud ?
- Existe-t-il des plans pour développer de nouvelles applications qui résident localement ou dans le nuage qui utilisera le cloud authentification ?

## <a name="evaluate-user-requirements"></a>Évaluer les besoins de l’utilisateur
Vous devez également évaluer les besoins des utilisateurs. Cette évaluation est importante de définir les étapes qui seront nécessaires pour l’intégration et aider les utilisateurs qu’ils la transition vers le cloud. Assurez-vous de répondre aux questions suivantes :

- Les utilisateurs accédant à applications en local ?
- Les utilisateurs accédant à applications dans le cloud ?
- Comment les utilisateurs généralement se connecter à leur environnement local ?
- Comment seront utilisateurs connexion dans le cloud ?

>[AZURE.NOTE]
Veillez à prendre des notes de chaque réponse et comprendre les raisons de la réponse. [Configuration requise de réponse à un incident déterminer](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) passera sur les options disponibles et les professionnels de l’informatique/inconvénients de chaque option.  En ayant répondu à ces questions que vous allez sélectionner option que le mieux à votre entreprise a besoin.

## <a name="next-steps"></a>Étapes suivantes
[Déterminer les besoins de synchronisation d’annuaire](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
