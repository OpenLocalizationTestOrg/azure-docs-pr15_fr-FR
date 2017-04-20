<properties
    pageTitle="Glossaire de Protection Azure Active Directory identité | Microsoft Azure"
    description="Glossaire de Protection identité Azure Active Directory"
    services="active-directory"
    keywords="protection d’identité Azure AD, la découverte application cloud, gestion des applications, sécurité, risque, le niveau de risque, vulnérabilité, stratégie de sécurité, glossaire"
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
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-identity-protection-glossary"></a>Glossaire de Protection identité Azure Active Directory 


### <a name="at-risk-user"></a>Exposés (utilisateur)  
Un utilisateur avec un ou plusieurs événements risque actif. 

### <a name="atypical-sign-in-location"></a>Emplacement de connexion pas courant   
Une connexion à partir d’un emplacement géographique qui n’est pas généralement utilisé pour l’utilisateur spécifique, les utilisateurs similaires ou le client.

### <a name="azure-ad-identity-protection"></a>Protection d’identité AD Azure    
Un module de sécurité d’Azure Active Directory qui offre une vue consolidée des événements de risque et vulnérabilités potentielles qui peuvent affecter des identités d’une organisation.

### <a name="conditional-access"></a>Accès conditionnel  
Une stratégie pour sécuriser l’accès aux ressources. Règles d’accès conditionnelle sont stockées dans Azure Active Directory et sont évaluées par Azure AD avant d’accorder l’accès à la ressource.  Exemples de règles incluent la restriction d’accès basé sur l’emplacement de l’utilisateur, méthode d’authentification appareil santé ou de l’utilisateur.

### <a name="credentials"></a>Informations d’identification     
Informations incluant identification et la preuve d’identification servant à accéder aux local et ressources réseau. Exemples d’informations d’identification sont les noms d’utilisateur et les mots de passe, des cartes à puce et certificats.

### <a name="event"></a>Événement   
Enregistrement d’une activité dans Azure Active Directory.

### <a name="false-positive-risk-event"></a>Faux positifs (événement risque) 
Statut de l’événement risque définir manuellement par un utilisateur de Protection d’identité, indiquant que l’événement risque a été soumis à l’enquête et a été incorrectement marqué comme un événement à risque.

### <a name="identity"></a>Identité    
Une personne ou entité qui doit être vérifiée au moyen d’authentification, en fonction de critères tels que le mot de passe ou d’un certificat.

### <a name="identity-risk-event"></a>Événement à risque identité     
Événement AAD qui a été marqué comme anormale par la Protection d’identité et indique qu’une identité a été déchiffrée.

### <a name="ignored-risk-event"></a>Ignoré (événement risque)    
Statut de l’événement risque définir manuellement par un utilisateur de Protection d’identité, indiquant que l’événement risque est fermé sans prendre une action de conversion.

### <a name="impossible-travel-from-atypical-locations"></a>Impossible de voyage à partir d’emplacements pas courant   
Un événement à risque déclenché lorsque deux se-ins pour le même utilisateur est détectées, où au moins une d’elles est depuis un emplacement de connexion pas courant, et le délai entre les signe-ins est inférieur à la durée minimale de voyage physiquement entre ces emplacements faut-il.  

###<a name="investigation"></a>Enquête    
Le processus de révision aux activités, journaux et autres informations pertinentes liées à un événement à risque pour décider si remediation ou réduction des étapes sont nécessaires, comprendre si et comment l’identité a été déchiffré et comprendre l’utilisation de l’identité compromise.

### <a name="leaked-credentials"></a>Informations d’identification perdues  

Un événement risque déclenché lorsque les informations d’identification utilisateur actuel (nom d’utilisateur et mot de passe) figurent validée publiquement dans le site web foncé nos chercheurs.

### <a name="mitigation"></a>Atténuation  
Une action à limiter ou supprimer la capacité d’un pirate pour exploiter un compromis identité ou un appareil sans restaurer l’identité ou l’appareil dans un état approuvé. Une atténuation ne résout pas les événements précédents risque associés à l’identité ou l’appareil.

### <a name="multi-factor-authentication"></a>Authentification multifacteur 
Méthode d’authentification qui requiert deux ou plusieurs méthodes d’authentification, qui peuvent inclure quelque chose que l’utilisateur a, ce certificat ; quelque chose connaît l’utilisateur, tels que les noms d’utilisateur, les mots de passe ou mots de passe ; attributs physiques, par exemple une empreinte numérique ; et attributs personnels, par exemple votre signature personnalisée.

### <a name="offline-detection"></a>Détection en mode hors connexion   
La détection des anomalies et d’évaluation du risque d’un événement comme tentative de connexion après le fait, pour un événement qui a déjà eu lieu.

### <a name="policy-condition"></a>Condition de stratégie    
Une partie d’une stratégie de sécurité qui définit les entités (groupes, utilisateurs, applications, plateformes d’appareil, États de périphérique, plages d’adresses IP, les types de clients) inclus dans la stratégie ou exclus de celle-ci.

### <a name="policy-rule"></a>Règle de stratégie     
La partie d’une stratégie de sécurité qui décrit les circonstances qui pourraient déclencher la stratégie et les actions exécutées lorsque la stratégie est déclenchée.

### <a name="prevention"></a>Prévention  
Une action pour protéger l’organisation via abuse d’une identité ou un appareil suspects ou en sorte d’être compromis. Une action de prévention non sécurisé le périphérique ou l’identité et ne résout pas les événements risque précédents.

### <a name="privileged-user"></a>Privilégié (utilisateur)
Un utilisateur ayant au moment de l’événement à risque, autorisations d’administrateur permanents ou temporaires pour une ou plusieurs ressources dans Azure Active Directory, par exemple un administrateur Global administrateur de facturation, administrateur de Service, administrateur d’utilisateur et mot de passe administrateur. 

###<a name="real-time"></a>En temps réel    
Voir détection en temps réel.

###<a name="real-time-detection"></a>Détection en temps réel  
La détection des anomalies et évaluation du risque d’un événement comme tentative de connexion avant l’événement sont autorisés à continuer.

### <a name="remediated-risk-event"></a>Résolues (événement risque)     
Statut de l’événement risque défini automatiquement par la Protection d’identité, indiquant que l’événement risque a été converti à l’aide de l’action de conversion standard pour ce type d’événement à risque. Par exemple, lorsque le mot de passe est réinitialisé, grand nombre d’événements risque qui indiquent que l’ancien mot de passe a été déchiffré est automatiquement convertis.

### <a name="remediation"></a>Mise à jour     
Une action pour sécuriser une identité ou un appareil qui ont été précédemment suspects ou connus pour être compromis. Une action de conversion restaure l’identité ou l’appareil êtes-vous sûr de vouloir et résout précédent événements risque associés à l’identité ou l’appareil.

### <a name="resolved-risk-event"></a>Résolu (événement risque)   
Statut de l’événement risque définir manuellement par un utilisateur de Protection d’identité, indiquant que l’utilisateur d’effectuer une opération de mise à jour appropriées en dehors de la Protection d’identité et que l’événement risque doit être considérées comme fermé.

###<a name="risk-event-status"></a>État de l’événement risque    
Une propriété d’un événement à risque, qui indique si l’événement est actif et si fermé, la raison de sa fermeture.

###<a name="risk-event-type"></a>Type d’événement risque  
Une catégorie de l’événement risque, indiquant le type d’anomalie qui a provoqué l’événement à prendre en considération risquée.

###<a name="risk-level-risk-event"></a>Niveau de risque (événement risque)  
Une indication (élevé, moyen ou bas) de la gravité de l’événement risque pour aider les utilisateurs de protection des données personnelles à classer par priorité les actions qu’ils prennent pour réduire les risques de leur organisation. 

###<a name="risk-level-sign-in"></a>Niveau de risque (connexion) 
Indication (élevé, moyen ou bas) de la probabilité que pour une spécifique se connecter, une autre personne tente d’utiliser identité de l’utilisateur.

###<a name="risk-level-user-compromise"></a>Niveau de risque (compromis utilisateur) 
Indication (élevé, moyen ou bas) de la probabilité qu’une identité a été compromise.

### <a name="risk-level-vulnerability"></a>Niveau de risque (vulnérabilité)  
Une indication (élevé, moyen ou bas) de la gravité du faille pour aider les utilisateurs de protection des données personnelles à classer par priorité les actions de qu’ils prennent pour réduire les risques de leur organisation.

### <a name="secure-identity"></a>Banque d’informations sécurisé (identité)   
Agir remediation tel qu’un nouveau mot de passe ou machine créer de nouvelles images pour restaurer une identité potentiellement compromise dans un état sans compromis.

### <a name="security-policy"></a>Stratégie de sécurité
Un ensemble de règles de stratégie et condition. Une stratégie peut être appliquée aux entités tels que les utilisateurs, groupes, applications, appareils, plateformes d’appareil, États de périphérique, plages d’adresses IP et les types de clients Auth2.0. Lorsqu’une stratégie est activée, elle est évaluée chaque fois qu’une entité incluse dans la stratégie est émise un jeton pour une ressource.

### <a name="sign-in-v"></a>Se connecter (v) 
S’authentifier à une identité dans Azure Active Directory.

### <a name="sign-in-n"></a>Se connecter (n) 
Le processus ou une action d’authentification d’une identité dans Azure Active Directory et de l’événement qui capture cette opération.

###<a name="sign-in-from-anonymous-ip-address"></a>Se connecter à partir de l’adresse IP anonyme    
Un événement à risque déclenchée après une connexion réussie à partir de l’adresse IP a été identifié comme une adresse IP du proxy anonyme.

###<a name="sign-in-from-infected-device"></a>Se connecter à partir d’appareil infecté 
Un événement à risque déclenché lorsqu’une connexion provient d’une adresse IP qui est connue devant être utilisé par un ou plusieurs périphériques compromis qu’activement essayez de communiquer avec un serveur robots.

###<a name="sign-in-from-ip-address-with-suspicious-activity"></a>Connexion à partir de l’adresse IP avec une activité suspecte 
Un risque déclenché après une connexion réussie à partir d’une adresse IP d’adresses avec un grand nombre de tentatives infructueuses de connexion entre plusieurs comptes d’utilisateurs sur un laps de temps.

###<a name="sign-in-from-unfamiliar-location"></a>Se connecter à partir d’emplacement inconnu 
Un événement à risque déclenché lorsqu’un utilisateur avec succès se connecte à partir d’un nouvel emplacement (IP, Latitude/Longitude et ASN).

###<a name="sign-in-risk"></a>Connexion à risque     
Voir risque niveau (connexion)

###<a name="sign-in-risk-policy"></a>Stratégie de connexion à risque  
Une stratégie d’accès conditionnelle qui évalue le risque à une connexion spécifique et applique minimisation basée sur les règles et conditions prédéfinies.

###<a name="user-compromise-risk"></a>Risque de compromis utilisateur     
Voir risque niveau (compromis utilisateur)

###<a name="user-risk"></a>Risque d’utilisateur    
Voir risque niveau (compromis utilisateur).

###<a name="user-risk-policy"></a>Stratégie de risque d’utilisateur     
Une stratégie d’accès conditionnelle qui estime la connexion et applique minimisation basée sur les règles et conditions prédéfinies.

###<a name="users-flagged-for-risk"></a>Utilisateurs de l’indicateur de risque   
Utilisateurs qui ont des événements risque qui sont soit active soit corrigée

###<a name="vulnerability"></a>Vulnérabilité    
Une condition dans Azure Active Directory qui rend le répertoire susceptibles d’être exploitable ou configuration ou menaces.


## <a name="see-also"></a>Voir aussi

- [Protection d’identité Azure Active Directory](active-directory-identityprotection.md)
