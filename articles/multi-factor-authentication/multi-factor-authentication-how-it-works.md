<properties 
    pageTitle="L’authentification multifacteur Azure - mode de fonctionnement"
    description="Azure l’authentification multifacteur permet divulgation d’informations l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple. Il fournit une sécurité supplémentaire en demandant une deuxième forme d’authentification et offre une authentification renforcée via une plage d’options de vérification facile."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

#<a name="how-azure-multi-factor-authentication-works"></a>Fonctionnement de l’authentification multifacteur Azure

La sécurité de l’authentification multifacteur réside dans son approche en couches. Compromettre plusieurs facteurs d’authentification présente un défi pour les pirates. Même si un intrus pour en savoir plus de mot de passe de l’utilisateur, il est inutile sans avoir également possession du périphérique approuvé. L’utilisateur doit perdre l’appareil, la personne qui la trouve ne pourrez utilisez-le, sauf si pourra également connaît le mot de passe.

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure l’authentification multifacteur permet divulgation d’informations l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple.  Il fournit une sécurité supplémentaire en demandant une deuxième forme d’authentification et offre une authentification renforcée via une plage d’options de vérification facile :

- appel téléphonique
- message texte
- notification de l’application mobile, ce qui permet aux utilisateurs de choisir la méthode préférez
- code de vérification de l’application mobile
- jetons de formule 3e partie

Pour plus d’informations au fait comment elle fonctionne voir la vidéo suivante.

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##<a name="methods-available-for-multi-factor-authentication"></a>Méthodes disponibles pour l’authentification multifacteur
Lorsqu’un utilisateur se connecte, une vérification supplémentaire est envoyée à l’utilisateur.  Voici une liste des méthodes pouvant être utilisées pour cette deuxième vérification.

Méthode de vérification  | Description
------------- | ------------- |
Appel téléphonique | Un appel est passé à leur demandant pour vérifier qu’ils sont se connecter en appuyant sur le signe # Smartphone d’un utilisateur.  Cela va terminer le processus de vérification.  Cette option est configurable et peut être modifiée pour un code que vous spécifiez.
Message texte | Un message texte recevrez Smartphone d’un utilisateur avec un code à 6 chiffres.  Entrez ce code dans pour terminer le processus de vérification.
Notification de l’application mobile | Une demande de vérification est envoyée à leur demandant complète la vérification en sélectionnant vérifier l’application mobile Smartphone d’un utilisateur. Ce problème survient si vous avez sélectionné la notification d’application en tant que votre méthode de vérification principal.  S’ils reçoivent cette lorsqu’ils ne sont pas se connecter, ils peuvent choisir signaler comme fraude.
Code de vérification avec l’application Mobile | Un code de vérification sera envoyé à l’application mobile qui s’exécute sur Smartphone d’un utilisateur.  Ce problème survient si vous avez sélectionné un code de vérification comme votre méthode de vérification principal.


##<a name="available-versions-of-azure-multi-factor-authentication"></a>Versions disponibles de l’authentification multifacteur Azure
L’authentification multifacteur Azure est disponible dans les trois différentes versions.  Le tableau ci-dessous décrit chacun de ces plus en détail.

Version  | Description
------------- | ------------- |
Authentification multifacteur pour Office 365 | Cette version fonctionne uniquement avec les applications Office 365 et est gérée à partir du portail Office 365. Afin que les administrateurs peuvent vous aider maintenant sécurisé leurs ressources Office 365 à l’aide de l’authentification multifacteur.  Cette version est fourni avec un abonnement Office 365.
Authentification multifacteur pour les administrateurs Azure | Le même sous-ensemble de fonctionnalités de l’authentification multifacteur pour Office 365 sera disponible sans frais à tous les administrateurs Azure. Chaque compte d’administration d’un abonnement Azure accessible maintenant une protection supplémentaire en activant cette fonctionnalité de l’authentification multifacteur principale. Pour un administrateur qui souhaite accéder Azure portail pour créer une machine virtuelle, un site web, à gérer le stockage, services mobiles ou tout autre Service Azure peuvent ajouter l’authentification multifacteur à son compte administrateur.
Authentification multifacteur Azure | L’authentification multifacteur Azure offre le jeu de fonctionnalités enrichie. <br><br>Il propose des options de configuration supplémentaires via le portail de gestion Azure, avancées création de rapports et support pour une plage en local et applications en nuage. L’authentification multifacteur Azure peut être achetée dans une licence autonome et est fournie dans Azure Active Directory Premium et Enterprise mobilité Suite. <br><br>Il peut également être acheté par de consommation en créant un fournisseur de l’authentification multifacteur Azure dans un abonnement Azure.
##<a name="feature-comparison-of-versions"></a>Comparaison des fonctionnalités des versions
Le tableau suivant ci-dessous fournit une liste des fonctionnalités qui sont disponibles dans les différentes versions de l’authentification multifacteur Azure.


Fonctionnalité  | Authentification multifacteur pour Office 365 (inclus dans Office 365 références SKU)|Authentification multifacteur pour les administrateurs Azure (inclus avec l’abonnement Azure) | Azure l’authentification multifacteur (inclus dans Azure AD Premium et entreprise mobilité Suite)
------------- | :-------------: |:-------------: |:-------------: |
Les administrateurs peuvent protéger les comptes avec l’authentification Multifacteur| * | * (Disponible uniquement pour les comptes d’administrateur Azure)|*
Application mobile comme un facteur deuxième|* | * | *
Appeler un deuxième facteur|* | * | *
SMS comme deuxième facteur|* | * | *
Mots de passe application pour les clients qui ne prennent pas en charge l’authentification Multifacteur|* | * | *
Administrateur contrôler les méthodes d’authentification| *| *| *
Mode code confidentiel| | | *
Alerte de fraude| | | *
Rapports de l’authentification Multifacteur| | | *
Contournement ponctuel| | | *
Message d’accueil personnalisé pour les appels téléphoniques| | | *
Personnalisation des ID de l’appelant pour les appels téléphoniques| | | *
Confirmation d’événement| | | *
Adresses IP autorisées| | | *
Suspendre l’authentification Multifacteur pour les appareils à retenir (Public Preview)| | | *
KIT DE DÉVELOPPEMENT DE L’AUTHENTIFICATION MULTIFACTEUR| | | *
Authentification Multifacteur pour les applications en local à l’aide de serveur de l’authentification Multifacteur| | | *


##<a name="how-to-get-azure-multi-factor-authentication"></a>Comment obtenir l’authentification multifacteur Azure

Si vous souhaitez que toutes les fonctionnalités proposées par l’authentification multifacteur Azure au lieu de simplement ceux fournis pour les utilisateurs d’Office 365 et les administrateurs Azure, il existe plusieurs options pour l’obtenir :

1.  Acheter des licences de l’authentification multifacteur Azure et les affecter à vos utilisateurs.
2.  Acheter des licences qui sont regroupés au sein de leur comme Azure Active Directory Premium ou entreprise mobilité Suite pour l’authentification multifacteur Azure et les affecter à vos utilisateurs.
3.  Créer un fournisseur de l’authentification multifacteur Azure au sein d’un abonnement Azure. Si vous n’avez pas un abonnement Azure, vous pouvez vous inscrire pour un abonnement d’évaluation Azure. Abonnements d’évaluation doit être converti en abonnements normales avant d’expiration.

Lorsque vous utilisez un fournisseur de l’authentification multifacteur Azure il existe deux modèles d’utilisation disponibles qui sont facturées via votre abonnement Azure :


- **Par utilisateur**. En règle générale pour les entreprises qui souhaitent activer l’authentification multifacteur pour un nombre constant d’employés devant régulièrement d’authentification.
- **Par authentification**. En règle générale pour les entreprises qui souhaitent activer l’authentification multifacteur pour un grand groupe d’utilisateurs externes qui ont rarement besoin d’authentification.

Pour connaître les prix voir [Azure l’authentification Multifacteur tarifs.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Sélectionnez la par siège ou un modèle de consommation appropriée pour votre organisation.   Puis de prise en main voir [Prise en main](multi-factor-authentication-get-started.md)
