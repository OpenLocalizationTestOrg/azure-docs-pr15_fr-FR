<properties
    pageTitle="Azure Active Directory hybride identité conception - déterminer les besoins de protection des données | Microsoft Azure"
    description="Lorsque vous planifiez votre solution identité hybride, identifiez les exigences de protection des données pour votre entreprise et les options sont disponibles pour mieux à ces exigences."
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

#<a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>Planifier pour renforcer la sécurité des données via la solution forte identité

La première étape pour protéger les données est identifier qui peut accéder à ces données et dans le cadre de ce processus, que vous devez disposer d’une identité solution peut s’intègre à votre système pour fournir des fonctionnalités d’authentification et d’autorisation. Authentification et autorisation sont souvent confondus avec eux et leurs rôles mal comprise. En réalité elles sont assez différentes, comme indiqué dans l’illustration ci-dessous :

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)
 
**Étapes de cycle de vie gestion appareil mobile**

Lorsque vous planifiez votre solution identité hybride, vous devez comprendre les exigences de protection des données pour votre entreprise et les options sont disponibles pour mieux à ces exigences.
 
>[AZURE.NOTE]
Une fois que vous avez terminé de planification de la sécurité des données, consultez [déterminer la configuration requise pour l’authentification multifacteur](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) pour vous assurer que vos sélections en ce qui concerne la configuration requise pour l’authentification multifacteur pas affectées par les décisions que vous avez effectuées dans cette section.

## <a name="determine-data-protection-requirements"></a>Déterminer les besoins de protection des données
Dans l’âge de la mobilité, la plupart des sociétés ont un objectif commun : permettre leur aux utilisateurs d’être productif où que vous soyez leurs appareils mobiles tout en local ou à distance afin d’améliorer la productivité. Alors que cela peut être le des objectifs communs, sociétés qui ont cette exigence seront également problème en ce qui concerne la quantité de menaces qui doivent être réduits afin de sécuriser les données de la société et mettre à jour de confidentialité de l’utilisateur. Chaque société peut avoir des exigences différentes à cet égard ; règles de conformité différentes qui varient selon le secteur d’activité agit la société, vous aboutissez à décisions de conception différents. 

Toutefois, il existe certains aspects de sécurité qui doivent être examinées et validés, quel que soit le secteur, elles sont décrites dans la section suivante.

## <a name="data-protection-paths"></a>Chemins d’accès de la protection des données

![](./media/hybrid-id-design-considerations/data-protection-paths.png)
 
**Chemins d’accès de la protection des données**

Dans le schéma ci-dessus, le composant identité sera la première personne à être vérifié avant d’accéder aux données. Toutefois, ces données peuvent être dans différents états pendant la fois qu’il a été consulté. Chaque nombre sur ce diagramme représente un chemin d’accès dans lequel les données peuvent être situées à un moment donné dans le temps. Ces nombres sont décrites ci-dessous :

1. Protection des données au niveau du périphérique.
2. Protection des données en cours de transfert.
3. Protection des données tout en reste en local.
4. Protection des données tout en reste dans le cloud.

Bien que la technique contrôles seront activer IT et protéger les données elles-mêmes sur chacun de ces phases pas directement proposées par la solution d’identité hybride, il est nécessaire que la solution d’identité hybride est capable d’exploiter les locaux et nuage de ressources de gestion des identités pour identifier l’utilisateur avant accorder l’accès aux données. Lorsque la planification de votre solution identité hybride garantir que les questions suivantes sont traitées en fonction des besoins de votre organisation :

## <a name="data-protection-at-rest"></a>Protection des données inactives
Quelle que soit l’endroit où les données sont au reste (périphérique, cloud ou en local), il est important de procéder à une évaluation pour mieux comprendre l’organisation a besoin à cet égard. Pour ce domaine, vérifiez que les questions suivantes sont posées :

- Votre société a besoin pour protéger les données au repos ?
 - Si Oui, est la solution d’identité hybride peut intégrer à votre infrastructure actuelle en local ?
 - Si Oui, est la solution d’identité hybride peut intégrer vos charges de travail situés dans le cloud ?
- La gestion des identités cloud est en mesure de protéger les informations d’identification de l’utilisateur et d’autres données stockées dans le cloud ?

## <a name="data-protection-in-transit"></a>Protection des données lors des transferts
Données lors des transferts entre l’appareil et le centre de données ou entre l’appareil et le cloud doivent être protégées. Toutefois, étant dans voies ne pas nécessairement un processus de communication avec un composant en dehors de votre service cloud ; Il déplace également, en interne, par exemple, entre deux réseaux virtuels. Pour ce domaine, vérifiez que les questions suivantes sont posées :

- Votre société a besoin pour protéger les données lors des transferts ?
 - Si Oui, est la solution d’identité hybride intègre aux contrôles sécurisés, tels que SSL/TLS ?
- La gestion des identités cloud conserve le trafic vers ou dans le magasin d’annuaires (dans et entre les centres de données) connecté ?


## <a name="compliance"></a>Conformité
Réglementations, lois et aux exigences de conformité réglementaire varient en fonction du secteur appartenant à votre société. Sociétés dans des secteurs réglementés haute doivent résoudre les problèmes de gestion des identités liés à des problèmes de conformité. Réglementations tels que SOA (SOX), la portabilité assurance et responsabilité Act (HIPAA), la loi Gramm-Leach-Bliley (GLBA) et le mode de paiement carte industrie données sécurité Standard (PCI DSS) sont très strictes en ce qui concerne l’identité et d’accès. La solution d’identité hybride votre société adoptera doit disposer des fonctionnalités principales qui répond à vos exigences d’une ou plusieurs de ces réglementations. Pour ce domaine, vérifiez que les questions suivantes sont posées :

- La solution d’identité hybride est conforme aux exigences réglementaires pour votre entreprise ?
- A la solution d’identité hybride intègre des fonctionnalités qui permettront à votre société doivent respecter les exigences réglementaires ? 
 
>[AZURE.NOTE]
Veillez à prendre des notes de chaque réponse et comprendre les raisons de la réponse. [Définir une stratégie de Protection des données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) seront passez en revue les options disponibles et les avantages et inconvénients de chaque option.  En ayant répondu à ces questions que vous allez sélectionner option que le mieux à votre entreprise a besoin.

## <a name="next-steps"></a>Étapes suivantes
 [Déterminer les besoins de gestion de contenu](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)


## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
