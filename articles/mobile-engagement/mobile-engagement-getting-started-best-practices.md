<properties 
    pageTitle="Azure Engagement Mobile prise en main Guide meilleures pratiques"
    description="Guide de mise en route mise en route pour Azure Mobile Engagement et des pratiques recommandées pour d’intégration" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="wesmc7777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/04/2016"
    ms.author="wesmc;ricksal"/>

# <a name="azure-mobile-engagement---getting-started-guide-with-best-practices"></a>Azure Engagement Mobile - Guide de démarrage avec les meilleures pratiques

## <a name="overview"></a>Vue d’ensemble

**L’écran mobile est un espace très entassé :** Dans 2013, une étude a détecté que l’appareil mobile moyenne avait 27 applications installées. Les utilisateurs généralement consacrées à 30 heures par mois dans leurs applications. La plupart ce laps de temps passé sur réseau social et le jeu (environ 20 heures). 2014, Android market avait environ 1,5 millions applications aux utilisateurs de choisir à partir de. L’Apple store contenait environ 1,2 millions applications. Utilisation de l’application mobile augmente toujours comme les développeurs compétition dans ce marché en pleine expansion. 

L’utilisateur mobile moyenne installer et désinstaller les applications avec haute fréquence en fonction de la modification des centres d’intérêt et des expériences dans l’application. Afin de déterminer la réussite d’une application, il est essentiel de connaître le nombre d’utilisateurs plus qu’installer votre application. Il est important de savoir comment votre application est utile et si cette tendance de l’utilisation peut changer. Les questions suivantes sont importantes :

- Sont vos utilisateurs début pour rechercher votre application inintéressants ou obsolète ? 
- Combien d’utilisateurs ont été arrêtés à l’aide de votre application tout ? 
- Dans l’application achats les plus populaires vers le haut ou vers le bas ?
- Utilisateurs échouent terminer le flux de travail en raison de problèmes avec l’application ou au manque d’intérêt ? 
- Pouvez vous maintenir votre application utile et pertinent en appuyant sur contenu actualisé à votre base de l’utilisateur ? 
- Feriez ce contenu actualisé soit la même pour tous les utilisateurs ou axée sur les segments utilisateur basées sur le comportement dans votre application ? 
 
Réponses aux questions similaires à ceux-ci peuvent vous aider à étendre la durée de vie et le chiffre d’affaires de votre application. Ils peuvent également vous aider définir et conserver votre base de l’utilisateur. 

Éléments multimédias liés applications ont tendance à avoir certains de la rétention plus élevée parmi les utilisateurs. L’une des raisons pour cela sont qu’ils sont constamment fournir du contenu actualisé aux utilisateurs. Adoption au plus tôt des notifications push utile dirigé vers un segment d’utilisateur a tendance à avoir un impact élevé sur rétention d’application. 

Le programme Azure Mobile Engagement est conçu pour vous aider à étendre la durée de vie et la rétention de votre application en fournissant une méthode pour collecter et analyser des informations détaillées sur l’utilisation de votre application. Il vous permettra de classer votre base en fonction du comportement d’utilisateurs et créer des campagnes activés pour proposer des notifications push et messages dans l’application à des segments d’utilisateurs identifiés. Indicateurs de performance clés (KPI) mesurent active comment vos utilisateurs sont avec différents aspects de votre application. Azure Engagement Mobile fournit les méthodes que vous devez déterminer ces indicateurs de performance clés. Il vous permet d’augmenter le retour sur votre investissement en fournissant l’infrastructure que nécessaire pour augmenter l’engagement avec votre application mobile. 

Pour tirer le meilleur parti Azure Mobile Engagement, vous devez commencer avec un plan d’engagement bien conçue. Votre offre vous aidera à identifier les données granulaires que vous devez être en mesure de catégorisation des utilisateurs. Cela peut être basée sur le comportement et des expériences dans l’application. Afin que votre offre soit réussie, il est recommandé de représenter clairement définir l’indicateur de performance clé décrivant les objectifs de votre application. Avec des indicateurs de performance clairement définis, vous pouvez facilement incorporer la logique nécessaire dans votre application pour recueillir des données à grains fins bien que vous utiliserez pour analyser et évaluer vos indicateurs de performance clés. Cette rubrique concerne un guide des meilleures pratiques pour la définition des indicateurs de performance clés que vous allez utiliser avec votre plan d’engagement. 


## <a name="step-1-define-your-kpis-to-fit-the-bet-model"></a>Étape 1 : Définir vos indicateurs de performance clés pour ajuster le modèle de meilleurs résultats


Définition d’indicateurs de performance clés correctement peut s’avérer difficile pour terminer. Applications conçues pour différents secteurs ont leurs propres caractéristiques et objectifs. Cela peut tendent à confondez pas votre approche. Pour éviter ce problème, des objectifs et des indicateurs de performance clés doivent être classées en trois catégories principales : **entreprise**, **Engagement**et **technique**. Voici ce que nous appelons le **modèle de meilleurs résultats**.

Un bon plan aura généralement objectifs avec les indicateurs de performance clés mesurent les réussites dans chacune des catégories suivantes du modèle de meilleurs résultats.


#### <a name="business-kpis"></a>Indicateurs de performance clés entreprise

Indicateurs de performance clés entreprise doivent être la partie plus simple pour générer. Vous avez probablement déjà défini ces éléments dans une forme lors de la planification de votre application mobile. Ces indicateurs de performance clés aident souvent chiffre d’affaires mesure et retour sur investissement vous application. La liste suivante fournit des indicateurs de performance clés entreprise qui peuvent vous aider lors de la définition de vos indicateurs de performance exemples :

- Indicateurs de performance clés de support professionnel
    - Un clic sur le nombre d’annonces
    - Numéro de page visites par utilisateur
    - Nombre d’abonnements en cours
- Indicateurs de performance clés jeu entreprise
    - Nombre d’achats dans l’application
    - Revenu moyen par utilisateur (ARPU)
    - Temps passé par session
    - Jours lus et en cours de niveau de jeu
- Indicateurs de performance clés de commerce électronique
    - Application jours utilisée
    - Revenu moyen par utilisateur (ARPU)
    - Montant moyen des panier lors de l’extraction
    - Catégorie de produit pour la plupart des affichages et achats
- KPI entreprise bancaire et d’assurance
    - Nombre de comptes
    - Fonctionnalités activées
    - Pages offre visités
    - Alertes cliqué ou activé      



#### <a name="engagement-kpis"></a>Indicateurs de performance clés engagement

Un indicateur de performance clé Engagement est un indicateur de performance mesurer l’engagement de vos utilisateurs. Les tendances dans cette zone aideront à déterminer la rétention de votre application. Voici quelques indicateurs de performance d’exemple pour ce type d’indicateur de performance clé :

- Utilisateurs actifs au cours des 7 derniers jours
- Nombre d’utilisateurs inactifs pour les 7 derniers jours
- Nombre d’utilisateurs qui ont utilisé pas l’application des 30 derniers jours  

Certains facteurs externes évidents peuvent influencer indicateurs dans cette zone. Par exemple, vous pouvez envisager un périphérique mobile avec un utilisateur à tout moment. Cela peut ou peut ne pas être vrai. Une application de jeu peut-être ont tendance à avoir autour des jours fériés lorsqu’un joueur peut être lu plus lors de la valeur travail scolaire facteur d’utilisation plus élevé.   

Bien définies indicateurs de performance clés dans cette catégorie doivent vous aider à mesurer la relation entre votre application et vos clients.



#### <a name="technical-kpis"></a>Indicateurs de performance clés techniques

Indicateurs de performance dans cette catégorie vous aident à déterminer si votre application est comporte correctement, en retrait ou cesse-t-il de fonctionner. Ces indicateurs peuvent mesurer l’état de votre application et déterminer les problèmes d’utilisation qui peuvent empêcher les utilisateurs d’utiliser l’application. Informations collectées pour cette catégorie peuvent également contenir des informations sur les performances qui seraient pertinentes aux équipes de marketing. Les données peuvent également s’avérer utiles de résoudre les problèmes en IT et aider à identifier des bogues permet aux équipes de support. 
 
Voici quelques exemples d’indicateurs de performance clés techniques :

- Nb et les informations d’exception non gérée ou géré 
- Horodatage de dernier incident
- Clic sur le bouton dernière ou la dernière page visitée
- Utilisation de la mémoire de l’application
- Fréquence d’application
- Version du système d’exploitation que l’application est en cours d’exécution
- Version de l’application

Définir ces indicateurs de performance clés pour aider à mesurer les performances de l’application et d’identifier les bogues potentiels. Cette indicateurs devraient aider à réduire le temps que nécessaire pour effectuer un correctif pour vos clients. Ils peuvent également vous permettent de définir un segment d’utilisateur qui a rencontré des problèmes d’un particulier. Vous pouvez utiliser cette segmentation des utilisateurs à créer des campagnes pour présenter des notifications importantes concernant les correctifs disponibles et promotions potentielles afin de récupérer la satisfaction client. 


#### <a name="playbook-exercise-1-create-your-kpi-dashboard"></a>Manuel exercice 1 : Créer votre tableau de bord d’indicateurs de performance clés

Lorsque vous définissez votre stratégie de marketing, vos indicateurs de performance clés doivent présenter un affichage pour chacun de vos objectifs principales. Ils doivent être des points de données clairement définis qui vous permet de recueillir les informations essentielles pour surveiller votre application et le comportement de l’utilisateur final.

Créer un tableau de bord d’indicateur de performance clé qui contient les informations suivantes

1.  Que sont les indicateurs de performance clés pour l’application ?
2.  Quelles sont les données pointant va utiliser pour représenter chaque indicateur de performance clé ?
3.  Où sont trouve ces données pour mon application (écran, paramètres, comme le système) ?
4.  Puis-je lire une séquence d’Engagement pour cet indicateur de performance clé ?

Vous pouvez utiliser la feuille de calcul **Générateur d’indicateur de performance clé** dans notre [Modèle manuel multimédia] [ Media Playbook link] pour exemples et des recommandations.



## <a name="step-2-your-engagement-program"></a>Étape 2 : Votre programme Engagement


Un programme de rédaction engagement mobile doit être considéré comme un composant clé de votre application. Cela doit absolument inclure un programme de bienvenue rédaction qui s’exécute pour un utilisateur pendant les premiers jours de l’utilisation de l’application. Cela a tendance à avoir un impact très positif sur engagement et la rétention de votre application. Études ont montré que la plupart des utilisateurs cesser d’utiliser une application les premiers jours après l’installation. Vous voulez efforçons atteindre ou dépasser intérêt conduite client attentes au plus tôt, tandis que l’utilisateur se concentre sur votre application. Vérifiez que vous présentez la valeur de clé et les avantages de votre application à vos clients. 


![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

Les notifications Push sont la meilleure approche pour engagements au plus tôt avec les utilisateurs d’appareils mobiles. Toutefois, excellent soyez attentif lorsque segmenter les utilisateurs pour les notifications push. Car une fois qu’un utilisateur semble il reçoit le courrier indésirable ou notifications inintéressantes, il peut avoir affecte-t-elle grave. En quelques clics, un utilisateur peut supprimer votre application jamais à retourner. L’utilisateur doit recevoir valeur intégrée dans l’application hautement personnalisée au lieu de spam générique.

Une fois que les utilisateurs sont activement, votre programme engagement permettent d’autres aspects de l’application du lecteur.

Par exemple, peut configurer une campagne qui demande vos utilisateurs à évaluer votre application actives. Étant donné que ce segment d’utilisateur n’est plus active et est l’expérience de la plupart avec votre application, vous vous attendez leur donner le contrôle d’accès plus précise. Une fois que vous avez une évaluation application élevé, il peut aider augmenter le téléchargement de votre application ainsi réduisant votre nouveau client d’acquisition organique.



#### <a name="engagement-sequence"></a>Séquence d’engagement


Un programme Engagement global inclut les séquences d’engagement différents. Chaque séquence vise à atteindre plusieurs objectifs.


###### <a name="life-push-sequence"></a>Séquence de push vie


Les objectifs pour une séquence de push vie sont différentes selon le cycle de vie d’engagement de l’utilisateur avec l’application. Un utilisateur particulier peut être très actif, inactif ou nouvelle. À différentes étapes d’un cycle de vie engagement, les utilisateurs peuvent bénéficier de votre contenu actualisé dans le formulaire des conseils ou des liens à la documentation. 

Par exemple un nouvel utilisateur peut besoin d’aide orientation à une application ou bénéficier d’une nouvelle inciter utilisateur semblable à ce qui suit la première fois qu’ils lancement l’application...

*« Donc content d’avez-vous bord ! N’oubliez pas à vous connecter pour obtenir votre 1er mois gratuits ! »*


###### <a name="behavioral-push-sequence"></a>Séquence de push comportement

La séquence push comportement a pour but d’augmenter l’utilisation basées sur le comportement de l’utilisateur collecté pour l’application.  

Par exemple, un utilisateur très actif d’une application de football fantaisie peut-être bénéficier d’engagement avec la notification push suivante...

*« Vous êtes un ventilateurs football graves John ! Connectez-vous à notre section NFL et gagnez libre accès à la SuperBowl ! »*


###### <a name="alerting-push-sequence"></a>Séquence push d’alerte

Utilisateurs sauront apprécier actualités intéressantes axées sur leurs centres d’intérêt. Une séquence d’alerte push améliore engagement en envoyant des alertes basées sur les centres d’intérêt un utilisateur a clairement montré. Il peut s’agir explicite lorsqu’un utilisateur sélectionne leurs propres centres d’intérêt dans l’application. Il peut également être déterminé implicitement en fonction des données collectées pendant l’interaction utilisateur avec l’application.

Par exemple, l’utilisateur d’une application de Commerce électronique peut régulièrement acheter une marque spécifique de café dont vous avez capturées avec une indicateur de performance clé d’entreprise. L’alerte suivante permet d’améliorer l’engagement de cet utilisateur avec l’application.
 
*« Bonjour Wes, une de votre marque favorite de café apparaîtra sur vente 25 % sur la première semaine de septembre 2015. Nous vous remercions d’avoir en tant que client et souhaité s’assurer que vous étiez prenant en charge ».*

###### <a name="rentention-push-sequence"></a>Séquence de push fixation

Cette séquence a pour but de conservation des utilisateurs à l’aide un campagnes de notification push répétitives afin de lecteur habitude normale d’engagement avec l’application. Cela permet d’augmenter la rétention application si l’utilisateur tire les interactions. 

Par exemple, l’utilisateur d’une application connexes sports pouvez recevoir les notifications suivantes hebdomadaire basée sur les équipes Favoris de l’utilisateur :

*« Pour tenter de gagner 200 points, accédez vote si la Yankees New York gagnez leur jeu cette semaine par rapport à Toronto bleu Jays ! ».*


#### <a name="the-3w-approach"></a>L’approche 3 w

Maîtriser les séquences push différents vous permettra de s’engager à des utilisateurs finaux. Toutefois, vous devez utiliser l’approche 3 w pour personnaliser les notifications. L’approche 3 w doit être appliqué qui, quand et comment faire pour chaque notification. Si vous clairement répondent à ces questions que vous notifications devez être correctement adaptée engagement.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)



###### <a name="who-the-user-segment-that-will-receive-messages"></a>Qui : L’utilisateur segmenter qui recevront les messages

Émission des notifications à vos utilisateurs à considérer comme un canal de communication très sensible. Vérifiez que les notifications que vous visent à envoyer à un segment d’utilisateur sont également limitées aux centres d’intérêt de ce segment d’utilisateur. Notification incorrectement routée est très probablement ont une incidence négative sur un utilisateur. Ils peuvent l’estiment le courrier indésirable et causer votre application en cours de désinstallation. 

Utilisez une combinaison de critères de signatures et techniques spécifiques lorsque vous définissez des segments d’utilisateurs qui reçoivent des notifications. Un exemple simple définition d’un segment d’utilisateur peut être similaire à la suivante :

« Tous les utilisateurs qui a démarré l’une application mobile pour la première fois 3 jours et avez visités à la page de connexion à deux reprises sans réellement terminer une connexion ».
 
Cette instruction permet d’identifier les données que nécessaires pour recueillir pour prendre en charge un scénario spécifique.


###### <a name="what-the-message-that-you-will-send"></a>Quoi : Le message que vous allez envoyer

**Nuance**

Dans votre engagements utiliser un ton qui est adapté à votre pour vos utilisateurs segmentés. Il s’agit certainement un bon moyen de vous connecter avec vos utilisateurs finaux et promouvoir l’intérêt d’un utilisateur dans votre application. 

**Redirection**

Une notification push peut être utilisée pour plus d’ouverture de l’application. Si le message de notification fournit un contexte telles que des actualités diffusion ou une promotion produit, cette notification peut approfondie lien directement au contenu correct dans l’application. Pour ce faire, vous devez créer un modèle d’URL pour laisser l’application gérer la redirection. Lorsque vous travaillez sur vos séquences d’engagement, il s’agit d’une étape importante qui ne doit pas être oubliée.

Redirection peut également être gérée pour d’autres systèmes. Par exemple, avec une URL de l’Action, il est possible rediriger les utilisateurs finaux dans de nombreux autres systèmes, y compris les éléments suivants :

- Un site Web
- Une boîte aux lettres avec messagerie déjà configuré
- Une zone de SMS
- Un service de conférence
- Directement dans le magasin d’applications d’évaluation de l’application. 

Cette offre de nombreuses possibilités à engager les utilisateurs finaux et à créer des règles automatiques afin d’améliorer les performances.


**Format/contenu**

Différents types et formats de notification Push :

1. **Annonces** : permet d’envoyer des messages publicitaires pour les utilisateurs à différents instants (déconnecter de l’application, dans l’application ou à tout moment).
2. **Sondages** : activé vous permettent de collecter les informations sur les utilisateurs finaux par leur poser des questions. Les réponses sont ensuite disponibles lors de la création des critères pour les utilisateurs finaux cible.
3. **Données pousse** : vous permet d’envoyer un fichier de données en base 64 ou binaire pour mettre à jour l’application. Les informations contenues dans un push de données sont envoyées à votre application pour personnaliser l’expérience des utilisateurs dans votre application. Votre application doit être conçue pour prendre en charge les données dans une diffusion de données.
4. **Vignettes (Windows Phone uniquement)** : activé vous permet d’utiliser le Service de Notification Push Microsoft (MPNS) pour envoyer une Notification Push Native contenant des données XML (pris en charge depuis le Kit de développement logiciel version 0.9.0. La charge utile finale pour les mosaïques ne peut pas dépasser 32 kilo-octets.). Le message s’affiche directement sur la vignette de votre carte.
5. **Mode d’affichage Web** : un affichage web est un contenu web contenant contextuel. Cette fenêtre contextuelle s’affiche lorsque l’utilisateur final a cliqué sur les notifications. Un affichage web permet que vous ayez plus grande interaction avec l’utilisateur final.
 
>[AZURE.NOTE] Assurez-vous que le contenu que vous envoyez comme les notifications push est conforme à la plateforme respectif (iOS, Android, Windows) instructions pour développer des applications et l’envoi des notifications push.

 


###### <a name="when-the-timing-of-your-campaign"></a>Quand : Le minutage de votre campagne

Lorsque le meilleur moment pour activer une campagne déclenche les notifications push ? Doit-il être manuelle ou automatique ? Doit être périodique ? Déterminer l’heure droite ou la fréquence est essentiel de s’engager utilisateurs avec les meilleurs résultats. Pour chaque séquence d’engagement et le scénario, vous devez spécifier quand sera le meilleur moment pour envoyer les notifications push. Voici quelques exemples possibles :

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Si vous envoyez des notifications de nombreux tous les jours, vous devez prendre une réflexion que vos utilisateurs penser que vos communications comme courrier indésirable. 

Azure Engagement Mobile fournit deux moyens pour aider à éviter des communications est perçues comme courrier indésirable. Tout d’abord, utilisez segmentation grains fins pour vous assurer que vous ne ciblez pas les mêmes utilisateurs. En outre, Azure Mobile Engagement fournit une fonctionnalité « quota ». Cette fonctionnalité peut limiter les notifications envoyées pour une campagne. Par exemple, définir un quota par défaut à 5 par semaine vous assurer qu’un utilisateur inclus comme partie du segment campagne utilisateur reçoit les notifications pas plus de 5 pour la semaine.





#### <a name="playbook-exercise-2-create-your-engagement-program"></a>Manuel exercice 2 : Créer votre programme engagement

Prenez le temps résumer vos objectifs et en définissant les campagnes que vous vous attendez à lire à l’aide de séquences spécifiques. Vérifiez que vous appliquez l’approche 3 w aux notifications dans vos campagnes marketing. 

Utiliser la feuille de calcul **Engagement programme** dans le [Modèle de manuel multimédia] [ Media Playbook link] pour exemples et des recommandations.


## <a name="step-3-app-integration"></a>Étape 3 : Intégration de l’application


#### <a name="create-a-tag-plan"></a>Créer un plan de balise

Pour intégrer Azure Mobile Engagement dans votre application, vous avez besoin créer un plan de balise. Le plan de balise est un jalon du projet. Il définit la relation entre les spécifications marketing, le flux de travail de l’application et les données réelles balise collectées dans l’application mesurer des indicateurs de performance clés. Il indique quels analytique vous seront en mesure de voir dans le portail. Il vous permet également de définir les segments d’utilisateurs et envoyer des notifications push activé de s’engager vos utilisateurs finaux. Une fois que vous disposez au plan d’indicateur défini, ajouter le code pour intégrer dans votre application est simple à l’aide du Kit de développement Azure Mobile Engagement.

Un plan de balise ne doit pas ajouter des balises à tous les éléments dans une application. Il doit inclure uniquement les données des balises qui fait partie de votre stratégie d’engagement mobile. Il s’agit probablement centralisée entre les applications. Le [Modèle manuel multimédia] [ Media Playbook link] fourni par Azure Mobile Engagement contribue à vous établissez un plan de balise avec une méthode donnée. Utiliser la feuille de calcul **Balise Plan** comme un guide pour la création de votre plan de balise.

Lorsque vous définissez une section de la balise dans la feuille de calcul, être très spécifiques. Ceci est très important éviter toute confusion. Détails de chaque scénario attendu dans chaque balise qui sera envoyé. Inclure le nom de l’activité dans lequel chaque balise est incorporé. Il doit être inclus dans la partie **Informative** de la feuille de calcul. La feuille de calcul de plan de balise doit être la référence principale pour la vérification de test. 

Dans la section **données à collecter** , votre équipe de développement doit rechercher les types, les noms, les valeurs et les paires supplémentaire informations clé/valeur requis pour chaque balise qui sera incorporé dans l’application.

Nous vous recommandons de révision du plan de balise avec toutes les équipes associés au projet. Apporter des corrections nécessaires et confirmer que tout est désactivée pour les équipes de développement et de marketing.

La feuille de calcul **instruction de travail** peut servir à guider que toutes les personnes impliquées dans le projet.


#### <a name="data-types"></a>Types de données

Il s’agit des types communs de prise en charge des données Azure Mobile engagement.

###### <a name="devices-and-users"></a>Périphériques et des utilisateurs

Azure Engagement Mobile identifie les utilisateurs en générant un identificateur unique pour chaque appareil. Cet identifiant est appelé identificateur d’appareil (ou ID de périphérique). Il est généré de façon à ce que toutes les applications en cours d’exécution sur ce même appareil partagent le même identificateur d’appareil.

###### <a name="sessions-and-activities"></a>Sessions et activités

Une session est une instance de l’application en cours d’exécution par un utilisateur. La session s’étend sur à partir du moment que l’utilisateur démarre l’application, jusqu'à l’arrêt.

Une activité est un regroupement logique d’un ensemble de choses que l’application peut faire au cours d’une session. Il s’agit généralement d’un écran particulier dans l’application, mais il peut s’agir que rien définies par la logique de l’application. Au minimum vous devez marquer chaque écran ou une activité pour votre application. Cela vous permettra de comprendre le chemin d’accès utilisateur.


###### <a name="events"></a>Événements

Événements sont utilisés pour signaler l’interaction utilisateur avec l’application. Elles peuvent être instantanées actions, telles que le partage du contenu ou lancer une vidéo. Marquage des événements pour fournir des collections de données qui montrent comment les utilisateurs interagir avec l’application. 

###### <a name="jobs"></a>Tâches

Les travaux sont utilisés pour signaler des actions qui ont une durée. Quelques exemples :

- Exécution d’appels API
- Durée d’affichage des annonces
- Durée des tâches en arrière-plan 
- Durée de processus d’achat
- L’affichage d’une vidéo


###### <a name="errors"></a>Erreurs

Erreurs sont utilisées pour signaler des problèmes détectés par l’application. Par exemple, actions de l’utilisateur incorrecte ou échecs des appels API.

###### <a name="application-information"></a>Informations sur l’application

Informations sur l’application (application-Info) sont utilisées pour baliser les données relatives à l’expérience utilisateur avec une application. Il est généré par interaction d’un utilisateur avec l’application. 

Pour une clé d’application-info donné, Azure Mobile Engagement uniquement effectue le suivi de la valeur la plus récente (aucun historique). Informations de l’application pour afficher l’état de votre application ou vos utilisateurs finaux. Par exemple le statut de connexion ou groupe Favoris produit d’un utilisateur.

###### <a name="crash-data"></a>Données de blocage

Se bloquer automatiquement collectées par les échecs d’application rapports Kit de développement Mobile Engagement ne pas gérées par l’application. Par exemple une exception non gérée qui se produit.


###### <a name="extra-data"></a>Données supplémentaires

Événements, les erreurs, les activités et les tâches peuvent être améliorées avec des paramètres. Il s’agit d’un développeur peut fournir en tant que données spécifiques à partir de l’application à des informations supplémentaires. Ceci est important pour la définition des permissions segmentation. 

Par exemple, la valeur d’une balise « article » vous permettra aux utilisateurs finaux segment basés sur qui affiché cet article particulier. Toutefois, qui peut ne pas être suffisamment. Il peut être préférable si ce même balise « article » inclus également supplémentaire-informations telles que « news_category » dans une activité. Elle peut être utile de déterminer dynamiquement les catégories favoris pour l’utilisateur. 

Informations de supplémentaire sont signalée comme une paire clé/valeur. Dans l’exemple de cette application support, les informations de supplémentaire pour « news_category » est la valeur de cette catégorie. Par exemple, « sports », « consommation » ou « politique ».





#### <a name="tag-and-sdk-integration"></a>Intégration de la balise et Kit de développement 

Pour obtenir des instructions étape par étape pour intégrer le Kit de développement Azure Mobile Engagement dans votre application, suivez la documentation [Engagement SDK intégration](mobile-engagement-windows-store-integrate-engagement.md) sur site Web Azure. Choisissez votre plateforme cible dans les liens situés en haut de la page.

Nous vous recommandons de créer des projets pour les deux applications basées sur Azure Mobile Engagement. Une pour le développement et de mise en attente de test et l’autre pour la mise en attente de production. Votre équipe informatique pouvez puis promouvoir depuis le test reclassement production lorsque le test d’acceptation utilisateur a réussi.



#### <a name="user-acceptance-testing-uat"></a>Test (UAT) d’acceptation utilisateur

Test d’acceptation utilisateur (UAT) consiste à s’assurer que tout fonctionne comme prévu. Flux de travail peuvent être effectuées et rassembler toutes les données en fonction de votre plan de balise :
 
- Marquage des informations à mettre en place en fonction de la documentation concepts AZME
- Toutes les informations que nécessaires sont collectées (y compris supplémentaire info, valeur application informations)
- Correspondances nomenclature en fonction de planifier la balise pour prendre en compte
- Il n’existe aucune balise en double envoyé

Tester tous les types de comportement de notification que vous avez incorporé dans votre application

- Insère l’annonces, sondages, se déconnecter de l’application et dans l’application
- Affichages de texte/du Web
- Badge de la mise à jour, catégories



#### <a name="setup"></a>Programme d’installation

La configuration d’Azure Mobile Engagement est très simple. Tous les documents liés à l’interface utilisateur sont disponible sur le site Web Azure Mobile Engagement, [comment naviguer dans l’interface utilisateur](mobile-engagement-user-interface-home.md).

Il est recommandé que vous démarrez en configurant les rôles droite et les appartenances à des rôles pour les utilisateurs de votre projet. Cela vous permet de gérer l’accès appropriés à la plateforme pour tous les utilisateurs. Vos rôles peuvent inclure :

- Administrateurs
- Développeurs
- Visionneuses 

Par la suite :
- Enregistrer votre ID de périphérique pour tester sur votre appareil.
- Accédez aux paramètres de votre compte et configurer le fuseau horaire disposer des graphiques et l’heure de remise de notification définie pour votre fuseau horaire.
- Accédez aux paramètres de votre application et enregistrez la « application-info « vous avez besoin pour l’utilisateur final cible à portée de main.

Pour plus d’informations sur l’exécution de votre première campagne de notification push, passez en revue [la mise en route utilisation et gestion des campagnes d’accéder à vos utilisateurs finaux](mobile-engagement-how-tos.md).



## <a name="conclusion"></a>Conclusion


Programmes d’engagement sont itératifs et vous devez améliorer constamment le vôtre lorsque vous testez ce qui convient le mieux pour votre application. 

Au départ, lors du développement d’une expérience engagement stratégies n’essayez pas de créer une stratégie de mission globale. Prendre une approche étape par étape identification vos indicateurs de performance clés et comment les utiliser. Stratégie d’engagement sera unique pour chaque application.

Une fois que vous avez développé certaines expérience songez à ajouter les éléments suivants à vos programmes d’engagement :

- Suivi : Acquérir des utilisateurs et que vous définissez probablement sources de collecte de données. Azure Engagement mobile peut être lié à des sources de collecte de données. Il vous permet de surveiller les performances de chaque source. Ces informations seront intéressantes pour optimiser votre investissement d’acquisition. 

- A / B test : il s’agit d’une partie essentielle du programme Engagement. Chaque application possède son propre caractéristiques de l’objet. Avec A / B test, vous pouvez améliorer votre programme engagement.

- GÉOLOCALISATION : Il s’agit d’une grande opportunité pour les marques. Grâce à cette fonctionnalité, vous pouvez accéder au bon endroit et à la fois. Nous vous recommandons de vérifier que vous avez collectées suffisamment données de comportement de l’utilisateur final avant de commencer à utiliser géolocalisation.

- Push de données : données push est un push invisible. Push de données permet de personnalisation de votre application basée sur le comportement de l’utilisateur final. Par exemple, si un segment d’utilisateur consulte souvent des produits de haute technologie, le propriétaire de l’application peut envoyer une campagne de données qui personnaliser la page d’accueil du contenu sophistiqués.



## <a name="next-steps"></a>Étapes suivantes

- [Créer un compte Azure Mobile Engagement](mobile-engagement-create.md).
- Visitez [définir votre stratégie d’Engagement Mobile](mobile-engagement-define-your-mobile-engagement-strategy.md) pour en savoir plus sur la définition de votre stratégie d’Engagement Mobile.



  

<!--Image references-->


<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
