<properties
    pageTitle="Azure Active Directory hybride identité conception - déterminer les besoins de synchronisation d’annuaire | Microsoft Azure"
    description="Identifier les besoins sont nécessaires pour la synchronisation de tous les utilisateurs entre on = locaux et cloud pour l’entreprise."
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

# <a name="determine-directory-synchronization-requirements"></a>Déterminer les besoins de synchronisation d’annuaire
La synchronisation consiste à fournir aux utilisateurs une identité dans le nuage en fonction de leur identité en local. Ou non qu’ils utiliseront synchronisée compte pour l’authentification ou fédérés, les utilisateurs doivent toujours avoir une identité dans le cloud.  Cette identité doit être conservées et mises à jour régulièrement.  Les mises à jour peuvent prendre différentes formes, des modifications de titre aux modifications de mot de passe.  

Commencer par estimer les organisations locale identité solution et utilisateur requises. Cette évaluation est importante de définir les impératifs techniques de comment identités utilisateur seront créées et mis à jour dans le cloud.  Pour la plupart des organisations, Active Directory est en local et est le répertoire local par les utilisateurs en synchronisé à partir de, cependant dans certains cas, il ne sera pas le cas.  

Assurez-vous de répondre aux questions suivantes :


- Vous avez une forêt AD, multiple ou aucun ?
 - Vous combien répertoires Azure AD serez synchronisés sur ?
 
    1. Vous utilisez le filtrage ?
    2. Vous disposez de plusieurs serveurs Azure AD Connect planifiées ?
  
- Avez-vous actuellement une synchronisation outil en local ?
  - Si Oui, signifie vos utilisateurs si les utilisateurs ont une répertoire/intégration virtuelle des identités ?
- Vous disposez de n’importe quel autre répertoire local que vous voulez synchroniser (par exemple, annuaire LDAP, base de données HR, etc.) ?
  - Souhaitez-vous faire tout GALSync ?
  - Quel est l’état actuel d’UPN dans votre organisation ? 
  - Avez-vous un répertoire différent utilisateurs s’authentifier ?
  - Votre entreprise utilise Microsoft Exchange ?
    - Prévoyez d’avoir un déploiement exchange hybride ?

Maintenant que vous avez une idée sur vos besoins en matière de synchronisation, vous devez déterminer quel outil est celui qui convient pour répondre à ces exigences.  Microsoft fournit plusieurs outils pour accomplir la synchronisation et intégration d’annuaire.  Voir le [tableau de comparaison des outils hybride identité répertoire intégration](active-directory-hybrid-identity-design-considerations-tools-comparison.md) pour plus d’informations. 
   
À présent que vous avez vos besoins en matière de synchronisation et de l’outil de cette phase pour votre société, vous devez évaluer les applications qui utilisent ces services d’annuaire. Cette évaluation est importante de définir les exigences techniques pour intégrer ces applications dans le cloud. Assurez-vous de répondre aux questions suivantes :

- Ces applications sont déplacées vers le cloud et utiliser ce répertoire ?
- Existe-t-il des attributs spécifiques que doivent être synchronisés dans le cloud afin que ces applications peuvent les utiliser correctement ?
- Ces applications devront être modifié pour tirer parti du cloud auth ?
- Ces applications continuent à live en local, tandis que les utilisateurs y accéder à l’aide de l’identité cloud ?

Vous devez également déterminer la synchronisation d’annuaire configuration requise et les contraintes de sécurité. Cette évaluation est importante obtenir une liste des exigences qui seront nécessaires pour créer et gérer des identités de l’utilisateur dans le cloud. Assurez-vous de répondre aux questions suivantes :

- Où le serveur de synchronisation se trouver ?
- Il sera joint au domaine ?
- Le serveur trouvera sur un réseau restreint derrière un pare-feu, par exemple une DMZ ?
  - Vous pourrez ouvrir les ports de pare-feu requis pour prendre en charge de la synchronisation ?
- Avez-vous un plan de récupération pour le serveur de synchronisation ?
- Avez-vous un compte disposant des autorisations appropriées pour toutes les forêts souhaité pour la synchronisation avec ?
 - Si votre société ne sait pas la réponse à cette question, consultez la section « Autorisations pour la synchronisation de mot de passe » dans l’article [installer le Service de synchronisation Azure Active Directory](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) et déterminer si vous possédez déjà un compte disposant de ces autorisations ou si vous avez besoin créer un.
- Si vous avez synchronisation multi-utilisateurs forêt est en mesure d’accéder à chaque forêt le serveur de synchronisation ?
 
>[AZURE.NOTE]
Veillez à prendre des notes de chaque réponse et comprendre les raisons de la réponse. [Configuration requise de réponse à un incident déterminer](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) accède via les options disponibles. En ayant répondu à ces questions que vous allez sélectionner option que le mieux à votre entreprise a besoin.

## <a name="next-steps"></a>Étapes suivantes
[Déterminer la configuration requise pour l’authentification multifacteur](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
