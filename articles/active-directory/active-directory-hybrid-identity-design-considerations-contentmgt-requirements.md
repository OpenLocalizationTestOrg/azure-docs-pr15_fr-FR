<properties
    pageTitle="Azure Active Directory hybride identité conception - déterminer les besoins de gestion de contenu | Microsoft Azure"
    description="Offre une vision comment déterminer les besoins de gestion de contenu de votre entreprise. En règle générale, lorsqu’un utilisateur possède son propre appareil il peut-être également plusieurs informations d’identification seront alternative en fonction de l’application qu’il utilise. Il est important de différencier quel contenu a été créé à l’aide des informations d’identification personnelles par rapport à ceux créés à l’aide des informations d’identification d’entreprise. Votre solution identité doit être en mesure d’interagir avec cloud services pour fournir une expérience transparente à l’utilisateur final lors de garantir la confidentialité et augmenter la protection contre les fuites de données."
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

# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Déterminer les besoins de gestion de contenu pour votre solution identité hybride

Comprendre les besoins de gestion de contenu pour votre entreprise peut affecter directement vos décisions sur quelle solution identité hybride à utiliser. Avec la prolifération des plusieurs appareils et permet aux utilisateurs d’apporter leur propre appareil ([BYOD](http://aka.ms/byodcg)), la société doit protéger ses propres données, mais il également doit conserver confidentialité de l’utilisateur. En règle générale, lorsqu’un utilisateur possède son propre appareil il peut-être également plusieurs informations d’identification seront alternative en fonction de l’application qu’il utilise. Il est important de différencier quel contenu a été créé à l’aide des informations d’identification personnelles par rapport à ceux créés à l’aide des informations d’identification d’entreprise. Votre solution identité doit être en mesure d’interagir avec cloud services pour fournir une expérience transparente à l’utilisateur final lors de garantir la confidentialité et augmenter la protection contre les fuites de données. 

Votre solution identité est exploitée par les différents contrôles techniques pour fournir la gestion de contenu comme indiqué dans l’illustration ci-dessous :
 
![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Contrôles de sécurité qui va exploiter votre système de gestion des identités**

En règle générale, en matière de gestion de contenu sera tirer parti de votre système de gestion des identités dans les zones suivantes :

- Confidentialité : identifier l’utilisateur qui possède une ressource et en appliquant les contrôles appropriés pour maintenir l’intégrité.
- Classification des données : identifier l’utilisateur ou le groupe et le niveau d’accès à un objet en fonction de sa classification. 
- Protection des données fuite : des contrôles de sécurité responsables de la protection des données pour éviter fuite doit interagir avec le système d’identité pour valider l’identité de l’utilisateur. Il est également importante pour but de piste d’audit.

>[AZURE.NOTE]
Lire [la classification des données pour la mise en cloud](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) pour plus d’informations sur les meilleures pratiques et des instructions pour la classification des données.

Lorsque la planification de votre solution identité hybride garantir que les questions suivantes sont traitées en fonction des besoins de votre organisation :

- Votre entreprise a-t-elle des contrôles de sécurité pour appliquer la confidentialité des données ?
 - Si Oui, ces contrôles de sécurité seront en mesure d’intégrer la solution d’identité hybride que vous vous apprêtez à adopter ?
- Est-ce que votre entreprise utilise la classification des données ?
 - Si Oui, est la solution en cours en mesure d’intégrer la solution d’identité hybride que vous vous apprêtez à adopter ?
- Votre entreprise a-t-elle actuellement une solution pour fuite de données ? 
 - Si Oui, est la solution en cours en mesure d’intégrer la solution d’identité hybride que vous vous apprêtez à adopter ?
- Votre entreprise doit-il auditer l’accès aux ressources ?
 - Si Oui, quel type de ressources ?
 - Si Oui, quel est le niveau d’information est-il nécessaire ?
 - Si Oui, où le journal d’audit doit se trouver ? En local ou sur le cloud ?
- Votre société a besoin pour chiffrer les messages électroniques qui contiennent des données sensibles (numéros de sécurité sociale, numéros de carte de crédit, etc.) ?
- Votre société a besoin pour chiffrer tous les documents/contenu partagé avec les partenaires externes ?
- Votre société a besoin appliquer des stratégies d’entreprise sur certains types de messages électroniques (ne pas répondre à tous, ne pas transférer) ?
 
>[AZURE.NOTE]
Veillez à prendre des notes de chaque réponse et comprendre les raisons de la réponse. [Définir une stratégie de Protection des données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) seront passez en revue les options disponibles et les avantages et inconvénients de chaque option.  En ayant répondu à ces questions que vous allez sélectionner option que le mieux à votre entreprise a besoin.


## <a name="next-steps"></a>Étapes suivantes
[Déterminer les exigences de contrôle d’accès](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
