<properties
    pageTitle="Protection d’identité Azure Active Directory | Microsoft Azure"
    description="Découvrez comment Protection Azure AD identité vous permet de limiter la capacité d’un pirate pour exploiter un appareil ou l’identité compromise et sécuriser une identité ou un périphérique qui a été précédemment suspects ou connu pour être compromis."
    services="active-directory"
    keywords="protection d’identité Azure AD, la découverte application cloud, gestion des applications, sécurité, risque, le niveau de risque, vulnérabilité, stratégie de sécurité"
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
    ms.date="10/26/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection"></a>Protection d’identité Azure Active Directory 

Azure Active Directory identité Protection est une fonctionnalité de l’édition Azure AD Premium P2 qui vous offre une vue consolidée des événements de risque et vulnérabilités potentielles qui peuvent affecter des identités de votre organisation. Microsoft a été la sécurisation identités sur le nuage pour plus de dix ans et bénéficiant d’une Protection Azure AD identité, Microsoft rend ces mêmes systèmes de protection disponibles pour les entreprises. Protection d’identité tire parti des fonctionnalités de détection d’annonce Azure existante anomalie (accessible à partir de rapports d’activité anormale d’Azure AD) et présente les types d’événements risque permettant de détecter anomalies en temps réel.



##<a name="getting-started"></a>Prise en main

La plupart des violations de sécurité avoir lieu lorsque les pirates accéder à un environnement en vol d’identité d’un utilisateur. Les pirates sont devenus plus efficaces pour exploiter les violations des règles tiers et en utilisant les attaques de phishing sophistiquées. Une fois qu’un intrus accède à un compte d’utilisateur doté de privilèges peu même, il est relativement simple pour pouvoir accéder aux ressources d’entreprise critiques grâce à des transferts latérale. Il est donc essentiel de protéger toutes les identités et, lorsqu’une identité est compromise, le fait empêcher l’identité compromise abusé en cours. 

Découverte des identités compromises n’est pas une tâche facile. Peut être effectué en, Protection d’identité peuvent vous aider : Protection d’identité utilise algorithmes d’apprentissage automatique adaptive et heuristique pour détecter des anomalies et événements indiquant qu’une identité a été déchiffrée des risques.
 
Protection d’identité à l’aide de ces données, génère des rapports et les alertes qui vous permet d’analyser ces événements risque et prendre remediation appropriée ou action d’atténuation.
 
Mais Azure Active Directory identité Protection est supérieure à un outil d’analyse et création de rapports. Protection d’identité en fonction des événements de risque, calcule un niveau de risque d’utilisateur pour chaque utilisateur, vous permettant de configurer des risques stratégies pour protéger automatiquement les identités de votre organisation.  Ces politiques en fonction du risque, en plus des autres contrôles d’accès conditionnelle fournies par Azure Active Directory et services, peuvent automatiquement bloquer ou proposent des actions de conversion adaptive qui incluent le mot de passe et l’application de l’authentification multifacteur.  

####<a name="explore-identity-protections-capabilities"></a>Explorez les fonctionnalités de Protection d’identité 

**Détection des événements de risque et risquées comptes :**  

- Types d’événement détection risque 6 à l’aide de règles heuristiques et l’apprentissage machine 

- Le calcul des niveaux de risque d’utilisateur

- Fournir des recommandations personnalisées pour améliorer la sécurité globale en mettant en surbrillance vulnérabilités



**Une solution événements risque :**

- Envoi des notifications pour les événements de risque

- Une solution événements risque à l’aide des informations pertinentes et contextuelles

- Fournir des flux de travail simple pour effectuer le suivi des enquêtes

- Permettant d’accéder facilement aux actions de conversion tels que de réinitialisation du mot de passe



**Stratégies d’accès conditionnelle risque :**

- Stratégie à limiter risquées signe-ins en blocage se-ins ou en exigeant défis l’authentification multifacteur.

- Stratégie de blocage ou des comptes d’utilisateurs risquée sécurisé

- Stratégie pour obliger les utilisateurs à s’inscrire à l’authentification multifacteur


## <a name="detection-and-risk"></a>Détection et les risques

### <a name="risk-events"></a>Événements de risque

Les événements risque sont des événements qui ont été marqués comme suspect par la Protection d’identité et indiquent qu’une identité a peut-être été compromise. Pour une liste complète des événements risque, voir [Types d’événements risque détectés par la Protection Azure Active Directory identité](active-directory-identityprotection-risk-events-types.md). 


### <a name="risk-level"></a>Niveau de risque

Le niveau de risque pour un événement à risque est une indication (élevé, moyen ou bas) de la gravité de l’événement risque. Le niveau de risque permet aux utilisateurs de Protection d’identité de classer par priorité les actions qu’ils à suivre pour éviter tout risque de leur organisation. La gravité de l’événement risque représente la puissance du signal comme PRÉDICTEUR de compromis identité, combiné avec la quantité de bruit il présente en général. 

- **Haute**: confiance élevée et événement à risque gravité élevée. Ces événements sont des indicateurs qui identité de l’utilisateur a été déchiffrée, et les comptes d’utilisateur concernés doivent être convertis immédiatement.

- **Support**: gravité élevée, mais un événement de risque de confiance inférieur, ou vice versa. Ces événements sont potentiellement dangereux et les comptes d’utilisateur concernés doivent être résolues.

- **Faible**: faible confiance et événement à risque faible gravité. Cet événement ne peut-être pas nécessiter une action immédiate, mais lorsqu’il est combiné avec d’autres événements à risque, peut-être fournir une indication forte que l’identité est déchiffrée. 


![Niveau de risque] (./media/active-directory-identityprotection/01.png "Niveau de risque")

 

Événements risque identifiées soit dans **en temps réel**, ou en traitement du résultat après l’événement risque a déjà pris placez (hors connexion). Actuellement la plupart des événements de risque dans Protection d’identité sont calculées en mode hors connexion et apparaissent-elles dans Protection d’identité au sein de 2 à 4 heures. Pendant qu’évaluée dans en temps réel, les événements de risque en temps réel apparaît dans la Console de Protection d’identité au sein de 5 à 10 minutes.

Plusieurs clients hérités ne prennent pas en charge prévention et détection d’événement risque en temps réel. Par conséquent, signe-ins à partir de ces clients ne peut pas être détectées ou bloquées en temps réel.


## <a name="investigation"></a>Enquête
Parcours via la Protection d’identité commence normalement par le tableau de bord Protection d’identité. 

![Mise à jour] (./media/active-directory-identityprotection/1000.png "Mise à jour")

Le tableau de bord permet d’accéder à :
 
- Rapports tels que **les utilisateurs indicateur de risque**et **événements risque** **vulnérabilités**
- Paramètres tels que la configuration des **Stratégies de sécurité**, **les Notifications** et **l’enregistrement de l’authentification multifacteur**
 

Il est généralement votre point de départ pour enquête, qui est le processus de révision aux activités, journaux et autres informations pertinentes liées à un événement à risque pour décider si une mise à jour ou réduction des étapes sont nécessaires, et comment l’identité a été déchiffré et comprendre l’utilisation de l’identité compromise.

Vous pouvez lier vos activités enquête aux [notifications](active-directory-identityprotection-notifications.md) Qu'azure Active Directory Protection envoie par courrier électronique.

Les sections suivantes fournissent des informations plus détaillées et les étapes qui sont associées à une enquête.  



## <a name="what-is-a-user-risk-level"></a>Qu’est un niveau de risque d’utilisateur ?

Un niveau de risque utilisateur est une indication (élevé, moyen ou bas) de la probabilité que l’identité de l’utilisateur a été déchiffrée. Il est calculé sur base les événements utilisateur risque qui sont associés à l’identité l’utilisateur. 

Le statut d’un événement de risque est **actif** ou **fermé**. Uniquement les événements de risque sont **actifs** contribuent au calcul risque utilisateur. 

Niveau de risque de l’utilisateur est calculé à l’aide des entrées suivantes :

- Événements risque actif ayant un impact sur l’utilisateur
- Niveau de risque de ces événements 
- Si les actions de mise à jour ont été prises 

![Risques pour l’utilisateur] (./media/active-directory-identityprotection/1001.png "Risques pour l’utilisateur")



Vous pouvez utiliser les niveaux de risque utilisateur pour créer des stratégies d’accès conditionnel pour empêcher les utilisateurs risquées de se connecter ou forcez pas en toute sécurité modifier leur mot de passe. 


## <a name="closing-risk-events-manually"></a>Fermeture d’événements risque manuellement

Dans la plupart des cas, vous faudra actions de conversion comme un mot de passe sécurisé pour les événements risque se ferme automatiquement. Toutefois, cela ne peut pas toujours possible.  
Il s’agit, par exemple, le cas échéant, lorsque :

- Un utilisateur avec des événements risque actif a été supprimé
- Votre enquête révèle qu’un événement à risque signalé a été effectuer par l’utilisateur légitime

Étant donné que les événements risque **Active** contribuent au calcul du risque utilisateur, vous devrez peut-être réduire manuellement un niveau de risque en fermant événements risque manuellement.  
Au cours de l’enquête, vous pouvez choisir d’effectuer l’une des actions suivantes pour modifier l’état d’un événement à risque :

![Actions] (./media/active-directory-identityprotection/34.png "Actions")

- **Résoudre** - si après avoir examiné un événement à risque, vous avez effectué une action de conversion approprié à l’extérieur de la Protection d’identité, et si vous pensez que l’événement risque doit être considérées comme fermé, marquez l’événement comme résolu. Résolu événements définira l’état de l’événement risque fermé et l’événement risque n’est plus contribuent au risque de l’utilisateur.

- **Marquer comme faux positifs** : dans certains cas, vous pouvez examiner un événement à risque et découvrir qu’il a été incorrectement marqué comme une opération risquée. Vous permet de réduire le nombre de ces occurrences en sélectionnant l’événement risque comme faux positifs. Cela vous aidera l’apprentissage algorithmes pour améliorer la classification des événements similaires à l’avenir de l’ordinateur. L’état des événements de faux positifs est **fermé** et qu’ils ne n’est plus contribuent à risque de l’utilisateur.

- **Ignorer** - si vous n’avez pas pris toute action de conversion, mais l’événement risque doit être supprimé de la liste active, vous pouvez marquer un événement à risque ignorer et l’état de l’événement sera fermé. Événements ignorés ne contribuent pas au risque d’utilisateur. Cette option doit être utilisée uniquement dans des circonstances exceptionnelles. 

- **Réactiver** - événements risque qui ont été fermé manuellement (en cliquant sur **résoudre**, **faux positif**ou sur **Ignorer**) peut être réactivé, définir le statut de l’événement en **actif**. Événements risque réactivé contribuent au calcul au niveau utilisateur risque. Événements risque fermés via mise à jour (par exemple, un mot de passe sécurisé Réinitialiser) ne peut pas être réactivés. 




**Pour ouvrir la boîte de dialogue configuration connexes**:

1. Dans la carte de **protection des données Azure AD personnelles** , sous **examiner**, cliquez sur **risques événements**.

    ![Réinitialiser votre mot de passe manuel] (./media/active-directory-identityprotection/1002.png "Réinitialiser votre mot de passe manuel")

2. Dans la liste **événements risque** , cliquez sur un risque.

    ![Réinitialiser votre mot de passe manuel] (./media/active-directory-identityprotection/1003.png "Réinitialiser votre mot de passe manuel")

2. Sur la carte risque, le menu contextuel d’un utilisateur.

    ![Réinitialiser votre mot de passe manuel] (./media/active-directory-identityprotection/1004.png "Réinitialiser votre mot de passe manuel")



### <a name="closing-all-risk-events-for-a-user-manually"></a>Fermeture manuellement tous les événements risque d’un utilisateur

Au lieu de fermer manuellement les événements risque d’un utilisateur individuellement, Azure Active Directory identité Protection également vous offre une méthode permettant de fermer tous les événements d’un utilisateur en un seul clic.


![Actions] (./media/active-directory-identityprotection/2222.png "Actions")

Lorsque vous cliquez sur **Ignorer tous les événements**, tous les événements sont fermées, et l’utilisateur concerné n’est plus exposés.



## <a name="remediating-user-risk-events"></a>Événements de risque correction utilisateur

Une mise à jour est une action pour sécuriser une identité ou un appareil qui a été précédemment suspects ou connu pour être compromis. Une action de conversion restaure l’identité ou l’appareil êtes-vous sûr de vouloir et résout précédent événements risque associés à l’identité ou l’appareil.

Pour remédier aux événements risque d’utilisateur, vous pouvez :

- Effectuer un mot de passe sécurisé pour remédier aux événements risque utilisateur manuellement 

- Configurer une stratégie de sécurité utilisateur risque pour atténuer ou corriger automatiquement les événements risque d’utilisateur

- Image du périphérique infecté  


### <a name="manual-secure-password-reset"></a>Réinitialisation du mot de passe sécurisé manuel

Une réinitialisation du mot de passe sécurisé est une mise à jour efficace pour de nombreux événements risque et lors de l’exécution, se ferme ces événements risque et automatiquement recalcule le niveau de risque utilisateur. Vous pouvez utiliser le tableau de bord Protection d’identité pour initier un mot de passe pour un utilisateur risqué. 

La boîte de dialogue connexe fournit deux méthodes différentes pour réinitialiser un mot de passe :

**Réinitialiser votre mot de passe** - Select **obliger l’utilisateur à réinitialiser votre mot de passe** pour autoriser l’utilisateur à consulter automatique récupérer l’utilisateur a enregistré pour l’authentification multifacteur. Au cours prochaine connexion de l’utilisateur, l’utilisateur est requises pour résoudre un problème de l’authentification multifacteur correctement et puis, obligé de modifier le mot de passe. Cette option n’est pas disponible si le compte d’utilisateur n’est pas encore enregistré l’authentification multifacteur.

**Mot de passe temporaire** - sélectionnez **Générer un mot de passe temporaire** immédiatement invalider le mot de passe existant et créer un nouveau mot de passe temporaire pour l’utilisateur. Envoyer le nouveau mot de passe temporaire vers une adresse de messagerie de secours pour l’utilisateur ou au responsable de l’utilisateur. Étant donné que le mot de passe est temporaire, l’utilisateur devra changer le mot de passe lors de la connexion.


![Stratégie] (./media/active-directory-identityprotection/1005.png "Stratégie")


**Pour ouvrir la boîte de dialogue configuration connexes**:

1. Dans la carte de **protection des données Azure AD personnelles** , cliquez sur **utilisateurs indicateur de risque**.

    ![Réinitialiser votre mot de passe manuel] (./media/active-directory-identityprotection/1006.png "Réinitialiser votre mot de passe manuel")


2. Dans la liste des utilisateurs, sélectionnez un utilisateur avec des événements au moins un risque.

    ![Réinitialiser votre mot de passe manuel] (./media/active-directory-identityprotection/1007.png "Réinitialiser votre mot de passe manuel")


2. Dans la carte de l’utilisateur, cliquez sur **Réinitialiser votre mot de passe**.

    ![Réinitialiser votre mot de passe manuel] (./media/active-directory-identityprotection/1008.png "Réinitialiser votre mot de passe manuel")





## <a name="user-risk-security-policy"></a>Stratégie de sécurité utilisateur risque

Une stratégie de sécurité utilisateur risque est une stratégie d’accès conditionnelle qui évalue le niveau de risque à un utilisateur spécifique et applique les actions d’atténuation et la résolution en fonction de conditions prédéfinies et les règles.


![Stratégie de ridk l’utilisateur] (./media/active-directory-identityprotection/1009.png "Stratégie de ridk l’utilisateur")


La Protection d’identité AD Azure vous permet de gérer l’atténuation et correction des utilisateurs indiquées de risque en ce qui vous permet :

- Définir les utilisateurs et groupes à que la stratégie s’applique : 

    ![Stratégie de ridk l’utilisateur] (./media/active-directory-identityprotection/1010.png "Stratégie de ridk l’utilisateur")


- Définir l’utilisateur risque seuil (faible, moyenne ou grande) qui se déclenche la stratégie : 

    ![Stratégie de ridk l’utilisateur] (./media/active-directory-identityprotection/1011.png "Stratégie de ridk l’utilisateur")


- Définir les contrôles à appliquer lorsque la stratégie déclenche :

    ![Stratégie de ridk l’utilisateur] (./media/active-directory-identityprotection/1012.png "Stratégie de ridk l’utilisateur")


- Changer l’état de votre stratégie :

    ![Stratégie de ridk l’utilisateur] (./media/active-directory-identityprotection/403.png "Inscription de l’authentification Multifacteur")


- Passez en revue et évaluer l’impact d’un changement avant son activation :

    ![Stratégie de ridk l’utilisateur] (./media/active-directory-identityprotection/1013.png "Stratégie de ridk l’utilisateur")


Choix d’un seuil **élevé** réduit le nombre de fois où une stratégie est déclenchée et réduit l’impact sur les utilisateurs.
Toutefois, il exclut les utilisateurs **basse** et **moyenne** indicateur de risque à partir de la stratégie, qui peut non sécurisé identités ou appareils qui ont été précédemment suspects ou connus pour être compromis.

Lorsque vous définissez la stratégie,

- Exclure les utilisateurs qui sont susceptibles de générer un grand nombre de fausses (les développeurs, les analystes de sécurité)

- Exclure des utilisateurs dans les paramètres régionaux dans lequel l’activation de la stratégie n’est pas pratique (par exemple aucun accès au service d’assistance)

- Utiliser un seuil **élevé** au cours de stratégie initiale déployer, ou si vous devez réduire les défis vus par les utilisateurs finaux.

- Utilisez un seuil **faible** si votre organisation exige une sécurité accrue. Sélection d’un seuil **faible** présente supplémentaires connexion défis liés aux utilisateurs, mais une sécurité accrue.

L’action par défaut pour la plupart des organisations consiste à configurer une règle pour un seuil de **support** à un équilibre entre facilité d’utilisation et la sécurité.

Pour une vue d’ensemble de l’expérience utilisateur connexes, voir :

- [Flux de récupération de compte compromis](active-directory-identityprotection-flows.md#compromised-account-recovery).  

- [Compte compromis bloqué flux](active-directory-identityprotection-flows.md#compromised-account-blocked).  


**Pour ouvrir la boîte de dialogue configuration connexes**:

1. Dans la carte de **protection des données Azure AD personnelles** , dans la section **configurer** , cliquez sur **stratégie risque de l’utilisateur**.

    ![Stratégie de ridk l’utilisateur] (./media/active-directory-identityprotection/1009.png "Stratégie de ridk l’utilisateur")






## <a name="mitigating-user-risk-events"></a>Réduction des événements risque d’utilisateur
Les administrateurs peuvent définir une stratégie de sécurité utilisateur risque pour empêcher les utilisateurs à se connecter en fonction du niveau de risque. 

Blocage une connexion :
 
- Empêche la génération de nouveaux événements de risque d’utilisateur pour l’utilisateur concerné

- Permet aux administrateurs manuellement mettre à jour les événements risque qui affectent identité de l’utilisateur et restaurer sur un état sécurisé



## <a name="what-is-a-sign-in-risk-level"></a>Qu’est un niveau de risque de connexion ?

Un niveau de risque de connexion est une indication (élevé, moyen ou bas) de la probabilité que pour un spécifique se connecter, une autre personne essaie de s’authentifier avec l’identité de l’utilisateur. Le niveau de risque de connexion est évalué au moment de l’une connexion et considère les événements risque et indicateurs détecté dans en temps réel pour cette connexion spécifique. 

## <a name="mitigating-sign-in-risk-events"></a>Réduction des événements de connexion à risque 
Une atténuation est une action à limiter la capacité d’un pirate pour exploiter un compromis identité ou un appareil sans restaurer l’identité ou l’appareil dans un état approuvé. Une atténuation ne résout pas les événements précédents connexion risque associés à l’identité ou l’appareil.

Vous pouvez utiliser accès conditionnel dans Azure AD identité Protection pour atténuer automatiquement des événements de connexion à risque. À l’aide de ces politiques, envisagez le niveau de risque de l’utilisateur ou la connexion à bloquer risquées signe-ins ou demander à l’utilisateur pour effectuer l’authentification multifacteur. Ces actions peuvent empêcher un utilisateur malveillant d’exploiter une identité volée pour endommager et peuvent vous donner du temps pour sécuriser l’identité. 


## <a name="sign-in-risk-security-policy"></a>Stratégie de sécurité risque de connexion

Une stratégie de connexion risque est une stratégie d’accès conditionnelle qui évalue le risque à une connexion spécifique et applique minimisation basée sur les règles et conditions prédéfinies.

![Connexion politique de risque] (./media/active-directory-identityprotection/1014.png "Connexion politique de risque")


La Protection d’identité AD Azure vous permet de gérer l’atténuation des compléments d’authentification risquées grâce aux :

- Définir les utilisateurs et groupes à que la stratégie s’applique : 

    ![Connexion politique de risque] (./media/active-directory-identityprotection/1015.png "Connexion politique de risque")


- Définir la connexion à niveau seuil de risque (faible, moyenne ou grande) déclenchant la stratégie : 

    ![Connexion politique de risque] (./media/active-directory-identityprotection/1016.png "Connexion politique de risque")


- Définir les contrôles à appliquer lorsque la stratégie déclenche :  

    ![Connexion politique de risque] (./media/active-directory-identityprotection/1017.png "Connexion politique de risque")
    

- Changer l’état de votre stratégie :

    ![Inscription de l’authentification Multifacteur] (./media/active-directory-identityprotection/403.png "Inscription de l’authentification Multifacteur")

- Passez en revue et évaluer l’impact d’un changement avant son activation : 

    ![Connexion politique de risque] (./media/active-directory-identityprotection/1018.png "Connexion politique de risque")


### <a name="what-you-need-to-know"></a>Ce que vous devez savoir

Vous pouvez configurer une stratégie de sécurité risque de connexion afin d’exiger l’authentification multifacteur :

![Connexion politique de risque] (./media/active-directory-identityprotection/1017.png "Connexion politique de risque")

Toutefois, pour des raisons de sécurité, ce paramètre fonctionne uniquement pour les utilisateurs qui ont déjà été enregistrés pour l’authentification multifacteur. Si la condition afin d’exiger l’authentification multifacteur est remplie pour un utilisateur qui n’est pas encore inscrit à l’authentification multifacteur, l’utilisateur est bloqué. 

Pour obtenir les meilleurs résultats, si vous souhaitez exiger l’authentification multifacteur pour se risquées-ins, vous devez :

1. Activer la [stratégie d’enregistrement de l’authentification multifacteur](#multi-factor-authentication-registration-policy) pour les utilisateurs.
2. Demandez aux utilisateurs affectés à se connecter dans une session non risquée pour effectuer un enregistrement de l’authentification Multifacteur

Ces étapes garantit que l’authentification multifacteur est requise pour une connexion risquée. 


### <a name="best-practices"></a>Meilleures pratiques

 
Choix d’un seuil **élevé** réduit le nombre de fois où une stratégie est déclenchée et réduit l’impact sur les utilisateurs.  
 
Toutefois, il exclut **basse** et **moyenne** se compléments d’indicateur de risque à partir de la stratégie, qui peut ne pas bloque un pirate d’exploiter une identité compromise. 

Lorsque vous définissez la stratégie, 

- Exclure des utilisateurs qui ne pas / ne peut pas contenir l’authentification multifacteur

- Exclure des utilisateurs dans les paramètres régionaux dans lequel l’activation de la stratégie n’est pas pratique (par exemple aucun accès au service d’assistance)

- Exclure les utilisateurs qui sont susceptibles de générer un grand nombre de fausses (les développeurs, les analystes de sécurité)

- Utiliser un seuil **élevé** au cours de stratégie initiale déployer, ou si vous devez réduire les défis vus par les utilisateurs finaux.

- Utilisez un seuil **faible** si votre organisation exige une sécurité accrue. Sélection d’un seuil **faible** présente supplémentaires connexion défis liés aux utilisateurs, mais une sécurité accrue.

L’action par défaut pour la plupart des organisations consiste à configurer une règle pour un seuil de **support** à un équilibre entre facilité d’utilisation et la sécurité.

 
La stratégie de connexion risque est la suivante :

- Appliquée à tout le trafic du navigateur et se-ins en utilisant l’authentification moderne.
- Pas appliqué aux applications à l’aide d’anciens protocoles de sécurité en désactivant le point de terminaison Web confiance à la IDP fédéré, par exemple AD FS.

La page **Événements risque** dans la console de Protection d’identité répertorie tous les événements :

- Cette stratégie a été appliquée à
- Vous pouvez consulter l’activité et déterminer si l’action était appropriée ou non 

Pour une vue d’ensemble de l’expérience utilisateur connexes, voir :

- [Récupération de connexion risquée](active-directory-identityprotection-flows.md#risky-sign-in-recovery) 

- [Risquée connexion bloqué](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  

- [Inscription de l’authentification multifacteur pendant une connexion risquée](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)  





**Pour ouvrir la boîte de dialogue configuration connexes**:

1. Dans la carte de **protection des données Azure AD personnelles** , dans la section **configurer** , cliquez sur **connexion politique des risques**.

    ![Stratégie de ridk l’utilisateur] (./media/active-directory-identityprotection/1014.png "Stratégie de ridk l’utilisateur")





## <a name="multi-factor-authentication-registration-policy"></a>Politique d’enregistrement de l’authentification multifacteur

L’authentification multifacteur Azure est une méthode de vérification qui vous soyez qui exige l’utilisation de plusieurs uniquement un nom d’utilisateur et un mot de passe. Il propose un second niveau de sécurité pour les compléments d’authentification utilisateur et les transactions.  
Nous vous recommandons d’exiger l’authentification multifacteur Azure pour les compléments d’authentification utilisateur, car il :

- Fournit une authentification forte avec un éventail d’options de vérification facile

- Joue un rôle clé dans la préparation de votre organisation de protéger et récupérer compromis compte

![Stratégie de ridk l’utilisateur] (./media/active-directory-identityprotection/1019.png "Stratégie de ridk l’utilisateur")



Pour plus d’informations, voir [Quel est l’authentification multifacteur Azure ?](../multi-factor-authentication/multi-factor-authentication.md)


La Protection d’identité AD Azure vous permet de gérer la sortie de l’enregistrement de l’authentification multifacteur en configurant une stratégie qui vous permet de : 



- Définir les utilisateurs et groupes à que la stratégie s’applique : 

    ![Stratégie de l’authentification Multifacteur] (./media/active-directory-identityprotection/1020.png "Stratégie de l’authentification Multifacteur")



- Définir les contrôles à appliquer lorsque la stratégie déclenche ::  

    ![Stratégie de l’authentification Multifacteur] (./media/active-directory-identityprotection/1021.png "Stratégie de l’authentification Multifacteur")


- Changer l’état de votre stratégie :

    ![Stratégie de l’authentification Multifacteur] (./media/active-directory-identityprotection/403.png "Stratégie de l’authentification Multifacteur")

- Afficher l’état actuel de l’enregistrement : 

    ![Stratégie de l’authentification Multifacteur] (./media/active-directory-identityprotection/1022.png "Stratégie de l’authentification Multifacteur")


Pour une vue d’ensemble de l’expérience utilisateur connexes, voir :

- [Flux d’enregistrement de l’authentification multifacteur](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  

- [Inscription de l’authentification multifacteur pendant une connexion risquée](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in).  





**Pour ouvrir la boîte de dialogue configuration connexes**:

1. Dans la carte de **protection des données Azure AD personnelles** , dans la section **configurer** , cliquez sur **l’enregistrement de l’authentification multifacteur**.

    ![Stratégie de l’authentification Multifacteur] (./media/active-directory-identityprotection/1019.png "Stratégie de l’authentification Multifacteur")





## <a name="next-steps"></a>Étapes suivantes

 - [Canal 9 : Azure AD et identité diaporama : aperçu de la Protection d’identité](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Types d’événements risque détectés par Azure Active Directory identité Protection](active-directory-identityprotection-risk-events-types.md)
 - [Vulnérabilités détectées par Azure Active Directory identité Protection](active-directory-identityprotection-vulnerabilities.md)
 - [Notifications d’Azure Active Directory identité Protection](active-directory-identityprotection-notifications.md)
 - [Azure manuel Protection d’identité Active Directory](active-directory-identityprotection-playbook.md)
 - [Glossaire Active Directory identité Protection Azure](active-directory-identityprotection-glossary.md)

 - [Connexion à des expériences avec Protection d’Azure AD identité](active-directory-identityprotection-flows.md)

 - [Activation de la Protection d’identité Azure Active Directory](active-directory-identityprotection-enable.md)
 - [Azure Active Directory identité Protection - comment débloquer les utilisateurs](active-directory-identityprotection-unblock-howto.md)

 - [Prise en main Azure Active Directory identité Protection et Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)


