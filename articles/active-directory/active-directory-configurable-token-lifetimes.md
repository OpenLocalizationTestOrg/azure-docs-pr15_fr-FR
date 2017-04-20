<properties
   pageTitle="Durée de vie des jetons configurable dans Azure Active Directory | Microsoft Azure"
   description="Cette fonctionnalité est utilisée par les administrateurs et les développeurs pour spécifier la durée de vie des jetons émis par Azure AD."
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/06/2016"
   ms.author="billmath"/>


# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Durée de vie des jetons configurable dans Azure Active Directory (Public Preview)

>[AZURE.NOTE]
>Cette fonctionnalité est actuellement dans la version d’évaluation.  Vous devez Préparez-vous à restaurer ou supprimer toutes les modifications.  Nous mettons cette fonctionnalité pour tout le monde à essayer pendant la présentation publique, cependant, certains aspects peuvent nécessiter un [abonnement Azure AD Premium](active-directory-get-started-premium.md) une fois que généralement disponibles.


## <a name="introduction"></a>Introduction
Cette fonctionnalité est utilisée par les administrateurs et les développeurs pour spécifier la durée de vie des jetons émis par Azure AD. Durée de vie des jetons peut être configurée pour toutes les applications dans un client, pour une application cliente multiples ou pour une identité Service spécifique dans un client.

Dans Azure Active Directory, un objet de stratégie représente un ensemble de règles appliquées à chaque application ou toutes les applications dans un client.  Chaque type de stratégie présente une structure unique avec un ensemble de propriétés qui sont ensuite appliquées aux objets auxquels ils sont affectés.

Une stratégie peut être désignée comme la valeur par défaut pour un client. Cette stratégie a puis effet dans une application qui se trouve au sein du client dans la mesure où il n’est pas remplacée par une stratégie avec une priorité élevée. Stratégies peuvent également être affectées à des applications spécifiques. L’ordre de priorité varie selon le type de stratégie.

Stratégies de durée de vie jeton peuvent être configurées pour actualiser jetons, les jetons d’accès, jetons de session et ID jetons.


### <a name="access-tokens"></a>Jetons d’accès
Un jeton d’accès est utilisé par un client pour accéder à une ressource protégée. Un jeton d’accès peut uniquement être utilisé pour une combinaison spécifique de l’utilisateur, des clients et des ressources. Les jetons d’accès ne peut pas être révoqués et sont valides jusqu'à son expiration. Un acteur malveillant qui a obtenu un jeton d’accès s’en servir pour l’étendue de sa durée de vie.  Durée de vie jeton ajustement access est un compromis entre amélioration des performances du système et augmenter le nombre d’heures que le client conserve access une fois le compte d’utilisateur est désactivé.  Améliorer les performances système sont atteint en réduisant le nombre de fois qu’un client doit acquérir un jeton d’accès novateur. 

### <a name="refresh-tokens"></a>Actualiser les jetons
Lorsqu’un client obtient un jeton d’accès pour accéder à une ressource protégée, il reçoit un jeton actualiser et un jeton d’accès. Le jeton d’actualisation sert à obtenir access/actualiser nouveau jetons paires expiration du jeton d’accès en cours. Actualiser les jetons sont liées à des combinaisons d’utilisateur et le client. Il peuvent être révoqués et leur validité est activée chaque fois qu’ils sont utilisés.

Il est important de faire une distinction entre les clients confidentielles et publics. Clients confidentielles sont des applications qui sont en mesure de stocker en toute sécurité un mot de passe du client, ce qui leur permet de prouver que provenant des demandes de l’application cliente et pas un acteur malveillant. Ces flux sont plus sécurisé, la durée de vie par défaut actualiser les jetons émis à ces flux sont plus élevés et ne peuvent pas être modifiés à l’aide de la stratégie.

En raison des limitations de l’environnement que les applications s’exécutent dans, clients publics ne peuvent pas stocker de façon sécurisée un mot de passe du client. Stratégies peuvent être définies sur les ressources d’empêcher actualisation jetons à partir de clients publics antérieurs à une période spécifiée d’obtenir une nouvelle paire de jeton d’accès/Actualiser (jeton Max Inactive heure d’actualisation).  En outre, stratégies peuvent être utilisées pour définir un délai au-delà duquel l’actualisation jetons ne sont plus accepté (Actualiser jeton Max Age).  Durée de vie jeton ajustement Actualiser permet de vous permettent de contrôler quand et la fréquence à laquelle l’utilisateur est requis pour entrer de nouveau les informations d’identification au lieu d’être en mode silencieux nouveau authentifié lors de l’utilisation d’une application cliente public.


### <a name="id-tokens"></a>Jetons ID
Jetons ID sont passés aux sites web et clients natifs et contiennent des informations de profil sur un utilisateur. Un jeton ID est lié à une combinaison spécifique d’utilisateur et le client. Jetons ID sont considérés comme valides jusqu'à l’expiration.  En règle générale, une application web correspond à un utilisateur de durée de vie de session dans l’application pour la durée de vie du jeton ID émis pour l’utilisateur.  Durée de vie jeton ajustement ID permet de vous permettent de contrôler la fréquence à laquelle l’application web expirer la session d’application et demander à l’utilisateur s’authentifier à nouveau avec Azure AD (en mode silencieux ou mode interactif).

### <a name="single-sign-on-session-token"></a>Jeton de session d’authentification unique
Lorsqu’un utilisateur s’authentifie avec Azure AD, une session est établie avec le navigateur et Azure AD de l’utilisateur.  La seule authentification Session jeton, sous la forme de des cookies, représente cette session. Il est important de noter que le jeton de session de l’authentification unique n’est pas lié à une application client/ressource spécifique. Jetons de session de l’authentification unique peuvent être révoqués et leur validité est activée chaque fois qu’ils sont utilisés.

Il existe deux types de jetons de session de l’authentification unique. Jetons de session permanente sont stockés les cookies permanentes par le navigateur et des jetons non permanent stockés en tant que les cookies de session (ils sont destruction lorsque le navigateur est fermé).

Jetons de session non permanente ont une durée de vie de 24 heures tandis que les jetons permanentes ont une durée de vie de 180 jours. Une fois que le jeton de session de l’authentification unique est utilisé dans sa période de validité, la période de validité est étendue un autre 24 heures ou jours 180. Si la session de l’authentification unique jeton n’est pas utilisé pendant sa période de validité, il est considéré comme expiré et seront ne sont plus acceptées. 

Stratégies peuvent être utilisées pour définir une période de temps après que la première session jeton a été émis au-delà duquel les jetons de session ne sont plus accepté (Session jeton Max Age).  Durée de vie jeton ajustement session permet de contrôler quand et la fréquence à laquelle l’utilisateur est nécessaire de retaper les informations d’identification au lieu d’être authentifié en mode silencieux lors de l’utilisation d’une application web.

### <a name="token-lifetime-policy-properties"></a>Propriétés de la stratégie durée de vie des jetons
Une stratégie de durée de vie jeton est un type d’objet de stratégie qui contient des règles de la durée de vie des jetons.  Les propriétés de la stratégie permettent de contrôler la durée de vie des jetons spécifiée.  Si aucune stratégie n’est définie, le système applique la valeur de durée de vie par défaut.


### <a name="configurable-token-lifetime-properties"></a>Propriétés de la durée de vie des jetons configurable
Propriété|Chaîne de la propriété stratégie|Affecte|Par défaut|Minimum|Valeur maximale|
----- | ----- | ----- |----- | ----- | ----- |
Durée de vie jeton Access|  AccessTokenLifetime|Les jetons d’accès, jetons ID, SAML2 jetons|1 heure|10 minutes|jour 1|
Actualisation Inactive jeton Max|    MaxInactiveTime |Actualiser les jetons |14 jours|10 minutes|    90 jours|
Âge Max jeton seul facteur actualisation|    MaxAgeSingleFactor  |Actualiser les jetons (pour tous les utilisateurs) |90 jours|10 minutes |Jusqu'à révoqué *|
Âge Max jeton multifacteur actualisation| MaxAgeMultiFactor|  Actualiser les jetons (pour tous les utilisateurs)| 90 jours|10 minutes|Jusqu'à révoqué *|
Âge Max jeton de Session seul facteur |MaxAgeSessionSingleFactor **    |Jetons de session (permanentes et non permanent)| Jusqu'à révoqué   |10 minutes |Jusqu'à révoqué *|
Âge Max jeton multifacteur Session| MaxAgeSessionMultiFactor ***|    Jetons de session (permanentes et non permanent)| Jusqu'à révoqué|  10 minutes| Jusqu'à révoqué *



- * 365 jours est la longueur maximale explicite pouvant être définies pour ces attributs.
- ** Si MaxAgeSessionSingleFactor n’est pas définie cette valeur prend la valeur MaxAgeSingleFactor. Si aucun paramètre est définie, la propriété prend la valeur par défaut (jusqu'à-révoqué).
- Si MaxAgeSessionMultiFactor n’est pas définie cette valeur prend la valeur MaxAgeMultiFactor. Si aucun paramètre est définie, la propriété prend la valeur par défaut (jusqu'à-révoqué).

### <a name="exceptions"></a>Exceptions
Propriété|Affecte|Par défaut|
----- | ----- | ----- |
Actualiser les jetons temps inactif Max (avec les informations de révocation insuffisante pour les utilisateurs fédérés)|Actualiser les jetons (émis pour les utilisateurs fédérés avec les informations de révocation insuffisante)|12 heures|
Actualisation Max jeton inactif (Clients confidentielles)| Actualiser les jetons (émis pour les Clients confidentielles)|90 jours|
Actualiser les jetons Max Age (émis pour les Clients confidentielles) |   Actualiser les jetons (émis pour les Clients confidentielles) |Jusqu'à révoqué

### <a name="priority-and-evaluation-of-policies"></a>Priorité et l’évaluation des stratégies

Stratégies de durée de vie jetons peuvent être créées et affectées aux applications spécifiques, des clients et des identités de service. Cela signifie qu’il est possible pour plusieurs stratégies à appliquer à une application spécifique. La stratégie de durée de vie jeton entre en vigueur suit ces règles :


- Si une stratégie est affectée à l’entité de service, il sera appliqué. 
- Si aucune stratégie n’est explicitement affectée à l’entité de service, une stratégie explicitement attribuée pour le client parent du service principal sera appliquée. 
- Si aucune stratégie n’est attribuée explicitement au service principal ou le client, la stratégie affectée à l’application est appliquée. 
- Si aucune stratégie n’a été affectée à l’entité de service, le client ou l’objet application, les valeurs par défaut seront appliquées (voir tableau ci-dessus).

Pour plus d’informations sur la relation entre les objets d’application et les objets principal de service dans Azure Active Directory, voir [Application et les objets principal de service dans Azure Active Directory](active-directory-application-objects.md).

Validité d’un jeton est évaluée au moment qu’il est utilisé. La stratégie avec la priorité la plus élevée dans l’application est accessible entre en vigueur.


>[AZURE.NOTE]
>Exemple
>
>Un utilisateur souhaite accéder à 2 applications web, A et B. 
>
>
>- Les deux applications sont dans un seul client parent. 
>- Durée de vie jeton stratégie 1 avec une Session jeton Max Age de 8 heures est définie comme valeur par défaut du client parent.
>- Application Web A est une application web utilisation normale et n’est pas liée à toutes les stratégies. 
>- Application Web B est utilisée pour les processus sensibles et son principal du service est liée à la stratégie de durée de vie jeton 2 avec un jeton de Session Max Age de 30 minutes.
>
>À 12:00 l’utilisateur ouvre une nouvelle session de navigateur et tente d’accéder aux application web a l’utilisateur est redirigé vers Azure AD et est invité à se connecter. Cela supprime un cookies avec un jeton de session dans le navigateur. L’utilisateur est redirigé précédent pour web application A avec un jeton d’ID qui leur permet d’accéder à l’application.
>
>À 12:15 PM, l’utilisateur essaie ensuite d’accéder à l’application web B. Le navigateur redirige vers Azure AD qui permet de détecter les cookies de session. Principal du service Web application B est lié à une stratégie de 1, mais il est également partie du client parent avec la stratégie par défaut 2. Stratégie 2 prend effet dans la mesure où les stratégies liées aux principaux de service ont une priorité plus élevée que les stratégies par défaut de client. Le jeton de session a été émis à l’origine dans les 30 minutes dernières afin qu’il est considéré comme valide. L’utilisateur est redirigé vers une application web B avec un jeton ID leur accorder l’accès.
>
>À 1:00 PM l’utilisateur essaie de la navigation jusqu'à web application A. L’utilisateur est redirigé vers Azure Active Directory. Application Web A n’est pas liée à toutes les stratégies, mais dans la mesure où se trouve dans un client avec la stratégie par défaut 1, cette stratégie entre en vigueur. La session des cookies sont détectés qui a été émise à l’origine dans les dernières heures 8 et que l’utilisateur est en mode silencieux redirigée vers une application web A avec un nouveau jeton ID sans avoir besoin de s’authentifier.
>
>L’utilisateur essaie immédiatement pour accéder à l’application web B. L’utilisateur est redirigé vers Azure Active Directory. Comme avant, stratégie 2 entre en vigueur. Lorsque le jeton a été émis plus de 30 minutes auparavant, l’utilisateur est invité à entrer à nouveau leurs informations d’identification, et une nouvelle session et jeton d’ID émis. L’utilisateur peut accéder puis application web B.

## <a name="configurable-policy-properties-in-depth"></a>Propriétés de la stratégie configurable : profondeur

### <a name="access-token-lifetime"></a>Durée de vie jeton Access

**Chaîne :** AccessTokenLifetime

**Affecte :** Jetons d’accès, jetons ID

**Résumé :** Cette stratégie contrôle la durée pendant laquelle access et des jetons ID pour cette ressource sont considérés comme valides. Réduire la durée de vie jeton d’accès réduit le risque d’un accès ou le jeton d’ID utilisé par un acteur malveillant pour une longue période de temps (comme elles ne peuvent pas être révoqués) mais également avoir un impact sur les performances comme les jetons devra être remplacé plus souvent.

### <a name="refresh-token-max-inactive-time"></a>Actualiser les jetons temps inactif max

**Chaîne :** MaxInactiveTime

**Affecte :** Actualiser les jetons

**Résumé :** Cette stratégie contrôle âge un jeton d’actualisation peut être avant un client n’est plus s’en servir à récupérer une nouvelle paire de jeton d’accès/actualiser lorsque vous tentez d’accéder à cette ressource. Dans la mesure où un nouveau jeton d’actualisation est renvoyé généralement qu'un jeton d’actualisation est utilisé, le client ne doit pas parvenus à toutes les ressources à l’aide du jeton d’actualisation en cours pour la période de temps avant que cette stratégie empêcher l’accès. 

Cette stratégie force les utilisateurs qui n’ont pas été actifs sur un client de s’authentifier à nouveau pour récupérer un nouveau jeton d’actualisation. 

Il est important de noter que le jeton Max Inactive heure d’actualisation doit être définie à une valeur inférieure à le Max âge jeton seul facteur et multifacteur actualiser jeton Max Age.

### <a name="single-factor-refresh-token-max-age"></a>Âge max jeton seul facteur actualisation

**Chaîne :** MaxAgeSingleFactor

**Affecte :** Actualiser les jetons

**Résumé :** Cette stratégie contrôle l’intervalle de temps un utilisateur pouvez continuer à utiliser des jetons actualisation pour obtenir access/actualiser nouveau paires jetons après la dernière fois qu’ils authentifiés correctement avec uniquement un facteur unique. Une fois qu’un utilisateur s’authentifie et reçoit un nouveau jeton d’actualisation, ils seront en mesure d’utiliser le flux de jeton d’actualisation (à condition que le jeton d’actualisation actuel n’est pas révoqué et qu’il n’est pas pas utilisé pendant plus de l’heure inactif) pour la période de temps spécifiée. À ce stade, les utilisateurs auront à s’authentifier à nouveau pour recevoir un nouveau jeton d’actualisation. 

Réduire l’âge max force aux utilisateurs de s’authentifier plus souvent. Dans la mesure où l’authentification unique facteur est considérée comme moins sûre que d’une authentification multifacteur, il est recommandé que cette stratégie est définie sur une valeur inférieure ou égale à multifacteur actualiser jeton Max âge stratégie.

### <a name="multi-factor-refresh-token-max-age"></a>Âge max jeton multifacteur actualisation

**Chaîne :** MaxAgeMultiFactor

**Affecte :** Actualiser les jetons

**Résumé :** Cette stratégie contrôle l’intervalle de temps un utilisateur pouvez continuer à utiliser des jetons actualisation pour obtenir access/actualiser nouveau paires jetons après la dernière fois qu’ils authentifiés correctement avec plusieurs facteurs. Une fois qu’un utilisateur s’authentifie et reçoit un nouveau jeton d’actualisation, ils seront en mesure d’utiliser le flux de jeton d’actualisation (à condition que le jeton d’actualisation actuel n’est pas révoqué et qu’il n’est pas pas utilisé pendant plus de l’heure inactif) pour la période de temps spécifiée. À ce stade, les utilisateurs auront à s’authentifier à nouveau pour recevoir un nouveau jeton d’actualisation. 

Réduire l’âge max force aux utilisateurs de s’authentifier plus souvent. Dans la mesure où l’authentification unique facteur est considérée comme moins sûre que d’une authentification multifacteur, il est recommandé que cette stratégie est définie sur une valeur supérieure ou égale à seul facteur actualiser jeton Max âge stratégie.

### <a name="single-factor-session-token-max-age"></a>Âge max jeton de session seul facteur

**Chaîne :** MaxAgeSessionSingleFactor

**Affecte :** Jetons de session (permanentes et non permanent)

**Résumé :** Cette stratégie indique combien de temps un utilisateur pouvez continuer à utiliser des jetons de session pour obtenir des nouveaux jetons ID et session après la dernière fois qu’ils ont été authentifiés avec uniquement un facteur unique. Une fois qu’un utilisateur s’authentifie et reçoit un jeton de nouvelle session, ils seront en mesure d’utiliser le flux de jeton de session (à condition que le jeton de session active n’est pas révoqué ou expiré) pour la période de temps spécifiée. À ce stade, les utilisateurs auront à s’authentifier à nouveau pour recevoir un jeton de nouvelle session. 

Réduire l’âge max force aux utilisateurs de s’authentifier plus souvent. Dans la mesure où l’authentification unique facteur est considérée comme moins sûre que d’une authentification multifacteur, il est recommandé que cette stratégie est définie sur une valeur inférieure ou égale à la stratégie d’âge Max jeton multifacteur Session.

### <a name="multi-factor-session-token-max-age"></a>Âge max jeton multifacteur session

**Chaîne :** MaxAgeSessionMultiFactor

**Affecte :** Jetons de session (permanentes et non permanent)

**Résumé :** Cette stratégie contrôle la durée pendant laquelle un utilisateur pouvez continuer à utiliser des jetons de session pour obtenir des nouveaux jetons ID et session après la dernière fois qu’ils ont été authentifiés avec plusieurs facteurs. Une fois qu’un utilisateur s’authentifie et reçoit un jeton de nouvelle session, ils seront en mesure d’utiliser le flux de jeton de session (à condition que le jeton de session active n’est pas révoqué ou expiré) pour la période de temps spécifiée. À ce stade, les utilisateurs auront à s’authentifier à nouveau pour recevoir un jeton de nouvelle session. 

Réduire l’âge max force aux utilisateurs de s’authentifier plus souvent. Dans la mesure où l’authentification unique facteur est considérée comme moins sûre que d’une authentification multifacteur, il est recommandé que cette stratégie est définie sur une valeur supérieure ou égale à la stratégie d’âge Max jeton seul facteur Session.

## <a name="sample-token-lifetime-policies"></a>Exemples de stratégies de durée de vie des jetons

La possibilité de créer et gérer la durée de vie des jetons pour applications, les identités de service et votre client global expose toutes sortes de nouveaux scénarios possibles dans Azure Active Directory.  Nous allons vous passez en revue quelques scénarios courants stratégie qui vous aidera à imposer des nouvelles règles pour :


- Durée de vie des jetons
- Max jeton inactif heures
- Jetons âge Max

Nous allons quelques scénarios, notamment :


- Gestion de stratégie par défaut d’un client
- Création d’une stratégie pour se connecter Web
- Création d’une stratégie pour les applications Native appel d’une API Web
- Gestion d’une stratégie de type Avancé 

### <a name="prerequisites"></a>Conditions préalables
Dans les exemples de scénarios nous allons être création, mise à jour, liaison et suppression stratégies sur applications, les principes de service et votre client global.  Si vous débutez dans Azure AD, l’extraction [cet article](active-directory-howto-tenant.md) pour vous aider à commencer avant de procéder à ces exemples.  


1. Pour commencer, téléchargez la dernière [Azure AD PowerShell applet de commande Aperçu](https://www.powershellgallery.com/packages/AzureADPreview). 
2.  Une fois que les applets de commande Azure AD PowerShell, exécutez la commande de connexion pour vous connecter à votre compte d’administrateur Azure AD. Vous devez effectuer cette action chaque fois que vous démarrez une nouvelle session.
        
        Connect-AzureAD -Confirm

3.  Exécutez la commande suivante pour afficher toutes les stratégies qui ont été créés dans votre client.  Cette commande doit être utilisée après la plupart des opérations dans les scénarios suivants.  Il vous permettra d’également obtenir l' **ID de l’objet** de vos stratégies. 
        
        Get-AzureADPolicy

### <a name="sample-managing-a-tenants-default-policy"></a>Exemple : Gestion de stratégie par défaut d’un client

Dans cet exemple, nous allons créer une stratégie permettant aux utilisateurs de se connecter moins fréquemment au sein de votre client entier. 

Pour ce faire, nous créer une stratégie de durée de vie des jetons pour les jetons actualiser seul facteur est appliqué entre votre client. Cette stratégie est appliquée à toutes les applications de votre client et chaque service principal qui n’a pas encore une stratégie auquel il. 

1.  **Créer une stratégie de durée de vie des jetons.** 

Définir le jeton d’actualiser seul facteur à sens « jusqu'à-révoqué » qu’il n’expire jusqu'à ce que l’accès est révoqué.  La définition de stratégie ci-dessous est ce que nous allons créer :
        
        @("{
          `"TokenLifetimePolicy`":
              {
                 `"Version`":1, 
                 `"MaxAgeSingleFactor`":`"until-revoked`"
              }
        }")

Puis exécutez la commande suivante pour créer cette stratégie. 

        
    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1, `"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName TenantDefaultPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy
        
Pour afficher votre nouvelle stratégie et obtenir son ObjectID, exécutez la commande suivante.

    Get-AzureADPolicy
&nbsp;&nbsp;2. **mettre à jour la stratégie**

Vous avez décidé que la première stratégie n’est pas assez stricte comme votre service nécessite et avez décidé de vos jetons seul facteur actualiser le point d’expirer en 2 jours. Exécutez la commande suivante. 
        
    Set-AzureADPolicy -ObjectId <ObjectID FROM GET COMMAND> -DisplayName TenantDefaultPolicyUpdatedScenario -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"2.00:00:00`"}}")
        
&nbsp;&nbsp;3. **vous avez terminé !** 

### <a name="sample-creating-a-policy-for-web-sign-in"></a>Exemple : Création d’une stratégie pour se connecter web

Dans cet exemple, nous allons créer une stratégie nécessitant vos utilisateurs s’authentifier plus fréquemment dans votre application Web. Cette stratégie définira la durée de vie des jetons/Id d’accès et le Max âge d’un jeton de Session multifacteur à l’entité de service de votre application web.

1.  **Créer une stratégie de durée de vie des jetons.**

Cette stratégie pour se connecter Web définira la durée de vie jeton/Id d’accès et Max seul facteur Session jeton Age à 2 heures.

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"02:00:00`",`"MaxAgeSessionSingleFactor`":`"02:00:00`"}}") -DisplayName WebPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicy

Pour afficher votre nouvelle stratégie et obtenir son ObjectID, exécutez la commande suivante.

    Get-AzureADPolicy
&nbsp;&nbsp;2. **affecter la stratégie à votre principal du service.**

Nous allons lier cette nouvelle stratégie avec un principal de service.  Vous devez également un moyen pour accéder aux **ObjectId** de votre service principal. Vous pouvez interroger le [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ou accédez à notre [Outil Explorer Graph](https://graphexplorer.cloudapp.net/) et connectez-vous à votre compte Azure AD pour afficher les identités de service de tous les votre client. 

Une fois que vous avez **ObjectId**, exécutez la commande suivante.
        
    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
&nbsp;&nbsp;3. **vous avez terminé !** 

 

### <a name="sample-creating-a-policy-for-native-apps-calling-a-web-api"></a>Exemple : Création d’une stratégie pour les applications natives appel d’une API Web

>[AZURE.NOTE]
>Liaison stratégies aux applications est désactivée.  Nous travaillons sur l’activation de ce peu de temps.  Cette page est mise à jour dès que la fonctionnalité est disponible.

Dans cet exemple, nous allez créer une stratégie nécessitant que les utilisateurs de s’authentifier inférieure et seront allongez la durée pendant laquelle qu'elles peuvent être inactives sans devoir s’authentifier à nouveau. La stratégie est appliquée à l’API Web, de cette manière lorsque l’application Native les demandes en tant que ressource que cette stratégie est appliquée.

1.  **Créer une stratégie de durée de vie des jetons.** 

Cette commande créera une stratégie stricte pour une API Web. 
        
    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"30.00:00:00`",`"MaxAgeMultiFactor`":`"until-revoked`",`"MaxAgeSingleFactor`":`"180.00:00:00`"}}") -DisplayName WebApiDefaultPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicy
         
Pour afficher votre nouvelle stratégie et obtenir son ObjectID, exécutez la commande suivante.

    Get-AzureADPolicy

&nbsp;&nbsp;2. **affecter la stratégie à votre API Web**.

Nous allons lier cette nouvelle stratégie avec une application.  Vous devez également un moyen pour accéder aux **ObjectId** de votre application. La meilleure façon de trouver de votre application **ObjectId** consiste à utiliser le [Portail Azure](https://portal.azure.com/). 

Une fois que vous avez **ObjectId**, exécutez la commande suivante.

    Add-AzureADApplicationPolicy -ObjectId <ObjectID of the App> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3. **vous avez terminé !** 

### <a name="sample-managing-an-advanced-policy"></a>Exemple : Gestion d’une stratégie de type Avancé 

Dans cet exemple, nous allons créer quelques stratégies pour montrer comment fonctionne le système de priorité, et comment vous pouvez gérer plusieurs stratégies appliquées à plusieurs objets. Cette formule donne une idée de la priorité des stratégies expliqué ci-dessus et vous aidera à gérer les scénarios plus complexes. 

1.  **Créer une stratégie de durée de vie des jetons**

Jusqu'à présent assez simple. Nous avons créé une stratégie client par défaut qui définit la durée de vie seul facteur actualiser jeton de 30 jours. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"30.00:00:00`"}}") -DisplayName ComplexPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy
Pour afficher votre nouvelle stratégie et téléchargez-le est ObjectID, exécutez la commande suivante.
 
    Get-AzureADPolicy

&nbsp;&nbsp;2. **affecter la stratégie à un Principal de Service**

Nous avons une stratégie dans l’intégralité du client.  Supposons que nous voulons préserver cette stratégie de 30 jours pour une identité de service spécifique, sans modifier la stratégie par défaut client pour être la limite supérieure de « jusqu'à-révoqué ». 

Tout d’abord, nous allons lier cette nouvelle stratégie avec notre principal du service.  Vous devez également un moyen pour accéder aux **ObjectId** de votre service principal. Vous pouvez interroger le [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ou accédez à notre [Outil Explorer Graph](https://graphexplorer.cloudapp.net/) et connectez-vous à votre compte Azure AD pour afficher les identités de service de tous les votre client. 

Une fois que vous avez **ObjectId**, exécutez la commande suivante.

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3. **définir l’indicateur IsTenantDefault à false à l’aide de la commande suivante**. 

    Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName ComplexPolicyScenario -IsTenantDefault $false
&nbsp;&nbsp;4. **créer une stratégie de client par défaut**

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName ComplexPolicyScenarioTwo -IsTenantDefault $true -Type TokenLifetimePolicy

&nbsp;&nbsp;5. **vous avez terminé !** 

Vous avez à présent la stratégie d’origine liée à votre principal du service et la nouvelle stratégie définir en tant que votre stratégie de client par défaut.  Il est important n’oubliez pas que stratégies appliquées aux entités de service ont priorité sur les stratégies de client par défaut. 


## <a name="cmdlet-reference"></a>Référence de l’applet de commande

### <a name="manage-policies"></a>Gérer les stratégies
Les applets de commande peut être utilisé pour gérer les stratégies.</br></br>



#### <a name="new-azureadpolicy"></a>Nouvelle AzureADPolicy
Crée une nouvelle stratégie.

    New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsTenantDefault <boolean> -Type <Policy Type> 

Paramètres|Description|Exemple|
-----| ----- |-----|
-Définition| Tableau de stringified JSON qui contient toutes les règles de la stratégie.|-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") 
Nom complet-|Chaîne du nom de la stratégie|Nom complet - MyTokenPolicy
-IsTenantDefault|Si la valeur true définit la stratégie en tant que stratégie par défaut du client si faux n’a aucun effet|-IsTenantDefault $true
-Type|Le type de stratégie, pour la durée de vie jeton toujours utiliser « TokenLifetimePolicy »|-TokenLifetimePolicy type
-AlternativeIdentifier [facultatif]|Définit un autre id à la stratégie.|MyAltId - AlternativeIdentifier
</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy         
Obtient tous les stratégies de rétention AzureAD ou stratégie spécifiée 
        
    Get-AzureADPolicy 

Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId [facultatif]|L’Id de la stratégie que vous voulez obtenir objet. |-ObjectId &lt;ObjectID de stratégie&gt; 
</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject         
Obtient tous les applications et les identités de service liées à une stratégie
        
    Get-AzureADPolicyAppliedObject -ObjectId <object id of policy> 

Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|L’Id de la stratégie que vous voulez obtenir objet.|-ObjectId &lt;ObjectID de stratégie&gt;
</br></br>

#### <a name="set-azureadpolicy"></a>Jeu de AzureADPolicy
Met à jour une stratégie existante
        
    Set-AzureADPolicy -ObjectId <object id of policy> -DisplayName <string> 
 
Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|L’Id de la stratégie que vous voulez obtenir objet.|-ObjectId &lt;ObjectID de stratégie&gt;
Nom complet-|Chaîne du nom de la stratégie|Nom complet - MyTokenPolicy
-Définition [facultative]|Tableau de stringified JSON qui contient toutes les règles de la stratégie.|-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") 
-IsTenantDefault [facultatif]|Si la valeur true définit la stratégie en tant que stratégie par défaut du client si faux n’a aucun effet|-IsTenantDefault $true
-Type [facultatif]|Le type de stratégie, pour la durée de vie jeton toujours utiliser « TokenLifetimePolicy »|-TokenLifetimePolicy type
-AlternativeIdentifier [facultatif]|Définit un autre id à la stratégie.|MyAltId - AlternativeIdentifier
</br></br>

#### <a name="remove-azureadpolicy"></a>Supprimer AzureADPolicy         
Supprime la stratégie spécifiée

     Remove-AzureADPolicy -ObjectId <object id of policy>

Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|L’Id de la stratégie que vous voulez obtenir objet.|-ObjectId &lt;ObjectID de stratégie&gt;
</br></br>

### <a name="application-policies"></a>Stratégies d’application
Les applets de commande peut être utilisée pour les stratégies d’application.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>AzureADApplicationPolicy ajouter         
Liens de la stratégie spécifiée à l’application

    Add-AzureADApplicationPolicy -ObjectId <object id of application> -RefObjectId <object id of policy>

Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|L’objet Id de l’Application.|-ObjectId &lt;ObjectID d’Application&gt; 
-RefObjectId|L’Id de la stratégie d’objet. |-RefObjectId &lt;ObjectID de stratégie&gt;
</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy        
Obtient la stratégie affectée à l’application

    Get-AzureADApplicationPolicy -ObjectId <object id of application>

Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|L’objet Id de l’Application.|-ObjectId &lt;ObjectID d’Application&gt; 
</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Supprimer AzureADApplicationPolicy        
Supprime une stratégie d’une application

    Remove-AzureADApplicationPolicy -ObjectId <object id of application> -PolicyId <object id of policy>

Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|L’objet Id de l’Application.|-ObjectId &lt;ObjectID d’Application&gt; 
-PolicyId| ObjectId de la stratégie.|-PolicyId &lt;ObjectID de stratégie&gt;
</br></br>

### <a name="service-principal-policies"></a>Stratégies principales du service
Les applets de commande peut être utilisé pour les stratégies principal de service.</br></br>

#### <a name="add-azureadserviceprincipalpolicy"></a>AzureADServicePrincipalPolicy ajouter         
Des liens de la stratégie spécifiée à une entité de service

    Add-AzureADServicePrincipalPolicy -ObjectId <object id of service principal> -RefObjectId <object id of policy>

Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|L’objet Id de l’Application.|-ObjectId &lt;ObjectID d’Application&gt; 
-RefObjectId|L’Id de la stratégie d’objet. |-RefObjectId &lt;ObjectID de stratégie&gt;
</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy        
Obtient n’importe quelle stratégie lié à l’entité de service spécifié

    Get-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>
 
Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|L’objet Id de l’Application.|-ObjectId &lt;ObjectID d’Application&gt; 
</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Supprimer AzureADServicePrincipalPolicy         
Supprime la stratégie de service spécifié principal

    Remove-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>  -PolicyId <object id of policy>
 
Paramètres|Description|Exemple|
-----| ----- |-----|
-ObjectId|L’objet Id de l’Application.|-ObjectId &lt;ObjectID d’Application&gt; 
-PolicyId| ObjectId de la stratégie.|-PolicyId &lt;ObjectID de stratégie&gt;
