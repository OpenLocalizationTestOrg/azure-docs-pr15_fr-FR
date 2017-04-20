
<properties
    pageTitle="Azure Active Directory hybride identité conception - déterminer les besoins d’incidents rResponse | Configuration requise pour Microsoft Azure "
    description="Déterminer les fonctionnalités d’analyse et création de rapports pour la solution d’identité hybride qui peuvent être exploitées par informatiques pour effectuer des actions pour identifier et atténuer un menaces"
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

# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Déterminer les besoins de réponse aux incidents pour votre solution identité hybride

Organisations de taille moyennes ou grandes probablement aura une [réponse aux incidents de sécurité](https://technet.microsoft.com/library/cc700825.aspx) en place pour vous aider informatique effectuer des actions en conséquence à un niveau de l’incident. Le système de gestion des identités est un composant important dans le processus de réponse, car elle peut être utilisée pour vous aider à identifier qui a effectué une action spécifique par rapport à la cible. La solution d’identité hybride doit être en mesure de fournir des fonctionnalités d’analyse et création de rapports qui peuvent être exploitées par informatiques pour effectuer des actions pour identifier et limiter une menace potentielle. Dans un plan de réponse par défaut, vous devrez les phases suivantes dans le cadre du plan :

1.  Évaluation initiale.
2.  Communication de l’incident.
3.  Limitation des dommages et réduction des risques.
4.  Identification des qu’il a été compromis et la gravité.
5.  Conservation des preuves.
6.  Notification aux personnes concernées.
7.  Récupération du système.
8.  Documentation.
9.  Évaluation des dommages et le coût.
10. Processus et offre la révision.

Lors de l’identification de ce qu’elle a été compromis et gravité phase, il est nécessaire identifier les systèmes qui ont été compromis, les fichiers qui ont accédé et déterminent le critère de diffusion de ces fichiers. Votre système d’identité hybride doit être en mesure de répondre à ces exigences pour vous aider à identifier l’utilisateur qui a créé ces modifications. 

## <a name="monitoring-and-reporting"></a>Surveillance et création de rapports
Le système d’identité autant de fois peut également aider de phase évaluation initiale principalement si le système a créé dans l’audit et les fonctionnalités de création de rapports. Lors de l’évaluation initiale, administrateur informatique doit être en mesure d’identifier une activité suspecte ou le système doit être en mesure de déclencheur qu'il automatiquement basé sur une tâche préconfigurée. De nombreuses activités peuvent indiquer une attaque possible, mais dans d’autres cas, un système mal configuré peut entraîner un certain nombre de fausses dans un système de détection des intrusions. 

Le système de gestion des identités doit aider les administrateurs informatiques pour identifier et signaler les activités suspectes. En règle générale, ces exigences techniques peuvent être satisfaites par tous les systèmes de surveillance et ayant une fonctionnalité de création de rapports que vous pouvez mettre en évidence des menaces potentielles. Utilisez les questions ci-dessous pour vous aider à concevoir votre solution identité hybride en tenant compte d’exigences incidents :

- Votre entreprise a une réponse aux incidents de sécurité en place ?
 - Si Oui, le système de gestion des identités actuelle est utilisé dans le cadre du processus ?
- Votre société a besoin pour identifier les suspects tentatives d’authentification des utilisateurs sur tous les appareils différents ?
- Votre société a besoin pour détecter les informations d’identification de l’utilisateur compromis potentiels ?
- Votre entreprise doit-il à un audit access et action de l’utilisateur ?
- Votre société a besoin de savoir quand un utilisateur réinitialiser son mot de passe ?

## <a name="policy-enforcement"></a>Application des stratégies

Lors de la limitation des dommages et phase de réduction des risques, il est important de réduire rapidement les effets réels et potentiels d’une attaque. Cette action vous prendra à ce stade peut faire la différence entre un mineur et un principal. La réponse exacte dépend de votre organisation et de la nature de l’attaque que vous rencontrer. Si l’évaluation initiale de conclure qu’un compte a été déchiffré, vous devrez appliquer une stratégie pour bloquer ce compte. C’est qu’un exemple où le système de gestion des identités est exploité. Utilisez les questions ci-dessous pour vous aider à concevoir votre solution identité hybride tout en tenant compte des mode d’application de stratégies pour répondre à un incident en cours :

- Votre entreprise a-t-elle stratégies en place pour empêcher les utilisateurs à partir d’access le réseau si nécessaire ?
 - Si Oui, la solution en cours s’intègre avec le système de gestion des identités hybride que vous vous apprêtez à adopter ?
- Votre société a besoin pour appliquer un accès conditionnel pour les utilisateurs qui se trouvent dans la mise en quarantaine ? 
 
>[AZURE.NOTE]
Veillez à prendre des notes de chaque réponse et comprendre les raisons de la réponse. [Définir une stratégie de protection des données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) seront passez en revue les options disponibles et les avantages et inconvénients de chaque option.  En ayant répondu à ces questions que vous allez sélectionner option que le mieux à votre entreprise a besoin.

## <a name="next-steps"></a>Étapes suivantes
[Définir la stratégie de protection des données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
