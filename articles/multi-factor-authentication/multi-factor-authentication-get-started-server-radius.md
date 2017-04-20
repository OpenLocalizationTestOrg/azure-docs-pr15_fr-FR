<properties 
    pageTitle="Serveur de l’authentification multifacteur l’authentification RADIUS et Azure"
    description="Il s’agit de la page de l’authentification multifacteur Azure qui aidera dans le déploiement d’authentification et de serveur de l’authentification multifacteur Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>



# <a name="radius-authentication-and-azure-multi-factor-authentication-server"></a>Serveur de l’authentification multifacteur l’authentification RADIUS et Azure

La section authentification RADIUS permet d’activer et configurer l’authentification RADIUS pour le serveur de l’authentification multifacteur Azure. RADIUS est un protocole standard pour accepter des demandes d’authentification et de traiter les demandes. Le serveur de l’authentification multifacteur Azure se comporte comme un serveur RADIUS et est inséré entre votre client RADIUS (par exemple, appareil VPN) et sur la cible d’authentification, qui pourrait être Active Directory (AD), un annuaire LDAP ou un autre serveur RADIUS, afin d’ajouter l’authentification multifacteur Azure. Pour l’authentification multifacteur Azure fonction, vous devez configurer le serveur de l’authentification multifacteur Azure afin qu’il peut communiquer avec les serveurs de client et la cible d’authentification. Le serveur de l’authentification multifacteur Azure accepte les demandes d’un client RADIUS valide les informations d’identification par rapport à la cible d’authentification, ajoute l’authentification multifacteur Azure et envoie une réponse au client RADIUS. L’authentification entière est établie uniquement si la l’authentification principale et l’authentification multifacteur Azure réussissent.

>[AZURE.NOTE]
>Le serveur de l’authentification Multifacteur prend uniquement en charge PAP (protocole d’authentification de mot de passe) et MSCHAPv2 (protocole de contrôle de Microsoft Authentication Protocol) RADIUS protocoles lorsqu’elles agissent comme un serveur RADIUS.  Autres protocoles tels que EAP (protocole d’authentification extensible) peuvent être utilisés lorsque le serveur de l’authentification Multifacteur se comporte comme un proxy RADIUS vers un autre serveur RADIUS qui prend en charge le protocole tel que Microsoft NPS.
></br>
>Lorsque vous utilisez d’autres protocoles dans cette configuration, jetons SMS et formule à sens unique ne fonctionnera pas car le serveur de l’authentification Multifacteur n’est pas en mesure d’initier une réponse RADIUS HIP réussie à l’aide de ce protocole.


![Authentification RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="radius-authentication-configuration"></a>Configuration de l’authentification RADIUS

Pour configurer l’authentification RADIUS, installez le serveur de l’authentification multifacteur Azure sur Windows server. Si vous avez un environnement Active Directory, le serveur doit être joint au domaine au sein du réseau. Utilisez la procédure suivante pour configurer le serveur de l’authentification multifacteur Azure :

1. Au sein du serveur de l’authentification multifacteur Azure cliquez sur l’icône de l’authentification RADIUS dans le menu de gauche.
2. Case à cocher Activer RADIUS d’authentification.
3. Sous l’onglet Clients modifiez l’ou les ports d’authentification et les ports de comptabilité si le service RADIUS de l’authentification multifacteur Azure doit être relié ports non standard pour écouter les demandes RADIUS à partir des clients qui seront configurés.
4. Cliquez sur Ajouter... bouton.
5. Dans la boîte de dialogue Ajouter un Client RADIUS, entrez l’adresse IP du matériel/serveur qui doit effectuer l’authentification pour le serveur de l’authentification multifacteur Azure, un nom de l’Application (facultatif) et un secret partagé. Le code secret partagé au devront être identique sur le serveur de l’authentification multifacteur Azure et le matériel/serveur. Le nom de l’Application apparaît dans les rapports de l’authentification multifacteur Azure et peut être affiché dans les messages d’authentification SMS ou une application Mobile.
6. Cochez la case de correspondance d’utilisateur nécessite l’authentification multifacteur si tous les utilisateurs ont été ou importées sur le serveur et soumis à l’authentification multifacteur. Si un nombre important d’utilisateurs n’ont pas encore été importé dans le serveur et/ou sera exempt de l’authentification multifacteur, laissez la case désactivée. Voir le fichier d’aide pour plus d’informations sur cette fonctionnalité.
7. Cochez la case Activer la secours jeton de formule si les utilisateurs utiliseront l’authentification de l’application mobile l’authentification multifacteur Azure et que vous voulez utiliser des codes secrets de formule comme une authentification secours la notification d’appel, SMS ou push hors du téléphone.
8. Cliquez sur le bouton OK.
9. Vous pouvez répéter les étapes 4 à 8 pour ajouter des clients RADIUS supplémentaires.
10. Cliquez sur l’onglet cible.
11. Si le serveur de l’authentification multifacteur Azure est installé sur un serveur à un domaine dans un environnement Active Directory, sélectionnez le domaine Windows.
12. Si les utilisateurs doivent être authentifiés par rapport à un annuaire LDAP, sélectionnez liaison LDAP. Lorsque vous utilisez liaison LDAP, vous devez cliquer sur l’icône d’intégration d’annuaire et modifier la configuration LDAP sous l’onglet Paramètres afin que le serveur pouvez lier à votre répertoire. Vous trouverez des instructions de configuration LDAP dans le guide de configuration du serveur Proxy LDAP.
13. Si les utilisateurs doivent être authentifiés par rapport à un autre serveur RADIUS, sélectionnez l’ou les serveurs RADIUS.
14. Configurer le serveur que le serveur proxy les requêtes RADIUS en cliquant sur l’ajout de... bouton.
15. Dans la boîte de dialogue Ajouter un serveur RADIUS Entrez l’adresse IP du serveur RADIUS et un secret partagé. Le code secret partagé au devront être identique sur le serveur de l’authentification multifacteur Azure et RADIUS server. Modifier le port d’authentification et le port de comptabilité si ports différentes sont utilisées par le serveur RADIUS.
16. Cliquez sur le bouton OK.
17. Vous devez ajouter le serveur de l’authentification multifacteur Azure comme un client RADIUS dans l’autre serveur RADIUS afin qu’il traite les demandes d’accès qui lui sont envoyées à partir du serveur de l’authentification multifacteur Azure. Vous devez utiliser le même secret partagé configuré sur le serveur de l’authentification multifacteur Azure.
18. Vous pouvez répéter cette étape pour ajouter d’autres serveurs RADIUS et configurer l’ordre dans lequel le serveur doit les appeler avec les boutons Monter et Descendre. La configuration du serveur de l’authentification multifacteur Azure est terminée. Le serveur est maintenant à l’écoute sur les ports configurés pour les demandes d’accès RADIUS à partir des clients configurés.   


## <a name="radius-client-configuration"></a>Configuration du Client RADIUS

Pour configurer le client RADIUS, suivez les instructions :

- Configurer votre appareil/serveur s’authentifier via RADIUS à l’adresse IP du serveur de l’authentification de plusieurs facteurs Azure, qui va agir en tant que le serveur RADIUS.
- Utiliser le même secret partagé qui a été configuré ci-dessus.
- Configurer le délai d’expiration RADIUS à 30 à 60 secondes pour qu’il est temps de valider les informations d’identification de l’utilisateur, effectuer l’authentification multifacteur, recevez sa réponse et puis répondre à la demande d’accès RADIUS.
