<properties 
    pageTitle="À distance de la passerelle de bureau et de serveur de l’authentification multifacteur Azure à l’aide de RADIUS"
    description="Il s’agit de la page de l’authentification multifacteur Azure qui aidera dans le déploiement de la passerelle Bureau à distance (RD) et serveur de l’authentification multifacteur Azure à l’aide de RADIUS."
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

# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>À distance de la passerelle de bureau et de serveur de l’authentification multifacteur Azure à l’aide de RADIUS

Dans de nombreux cas, passerelle Bureau à distance utilise le serveur NPS local pour authentifier les utilisateurs. Ce document décrit comment router demande RADIUS déconnecter à partir de la passerelle Bureau à distance (via le serveur NPS local) sur le serveur de l’authentification multifacteur.

Le serveur de l’authentification multifacteur doivent être installé sur un serveur différent, qui sera proxy puis la demande RADIUS revenir au serveur NPS sur le serveur de passerelle Bureau à distance. Une fois NPS valide le nom d’utilisateur et le mot de passe, il renverra une réponse sur le serveur de l’authentification multifacteur qui effectue le second facteur d’authentification avant de retourner un résultat à la passerelle.





## <a name="configure-the-rd-gateway"></a>Configuration de la passerelle Bureau à distance

La passerelle Bureau à distance doit être configurée pour envoyer l’authentification RADIUS à un serveur de l’authentification multifacteur Azure. Une fois que la passerelle Bureau à distance a été installé, configuré et est travail, accédez aux propriétés de la passerelle Bureau à distance. Accédez à l’onglet RD Lettrine Store et modifiez-le pour utiliser un serveur Central exécutant NPS au lieu de serveur Local exécutant NPS. Ajoutez un ou plusieurs serveurs de l’authentification multifacteur de Azure comme serveurs RADIUS et spécifiez un secret partagé pour chaque serveur.





## <a name="configure-nps"></a>Configurer NPS

La passerelle RD utilise NPS pour envoyer la demande RADIUS pour l’authentification multifacteur Azure. Un délai d’expiration doit être modifié pour éviter la passerelle RD d’arriver à expiration avant la fin de l’authentification multifacteur. Utilisez la procédure suivante pour configurer NPS.

1. Dans NPS, développez le menu RADIUS Clients et serveur dans la colonne de gauche et cliquez sur groupes de serveurs RADIUS distants. Accédez aux propriétés du groupe du serveur de passerelle TS. Modifier les serveurs RADIUS affiché et accédez à l’onglet l’équilibrage de charge. Modifier le « nombre de secondes sans réponse avant que la requête est considérée comme ignorés » et « Nombre de secondes entre les demandes avant que le serveur est identifié comme non disponible » à 30 à 60 secondes. Cliquez sur l’onglet authentification/compte et vérifiez que les ports RADIUS spécifiés faire correspondre les ports que le serveur de l’authentification multifacteur écoute.
2. NPS doit également être configuré pour recevoir des authentifications RADIUS du serveur de l’authentification multifacteur Azure. Cliquez sur Clients RADIUS dans le menu de gauche. Ajouter le serveur de l’authentification multifacteur Azure comme un client RADIUS. Donnez un nom convivial et spécifiez un secret partagé.
3. Développez la section stratégies dans le volet de navigation gauche, cliquez sur stratégies de demande de connexion. Il doit contenir une stratégie de demande de connexion appelée stratégie d’autorisation de passerelle TS qui a été créé lors de la configuration de passerelle Bureau à distance. Cette stratégie transfère demandes RADIUS sur le serveur de l’authentification multifacteur.
4. Copiez cette stratégie pour créer un nouveau. Dans la nouvelle stratégie, ajoutez une condition qui correspond au nom convivial Client avec le nom convivial définie à l’étape 2 ci-dessus pour le client Azure multifacteur d’authentification serveur RADIUS. Remplacer le fournisseur d’authentification par ordinateur Local. Cette stratégie garantit que lorsqu’une demande de rayon est reçue à partir du serveur de l’authentification multifacteur Azure, l’authentification se produise localement au lieu d’envoyer une demande RADIUS sur le serveur de l’authentification multifacteur Azure entraînant une condition de boucle. Pour éviter la condition de boucle, celle-ci doit être commandé au-dessus de la stratégie d’origine de transfert sur le serveur de l’authentification multifacteur.

## <a name="configure-azure-multi-factor-authentication"></a>Configurer l’authentification multifacteur Azure


--------------------------------------------------------------------------------



Le serveur de l’authentification multifacteur Azure est configuré comme proxy RADIUS entre passerelle Bureau à distance et NPS.  Il doit être installé sur un serveur à un domaine qui est différent du serveur de passerelle Bureau à distance. Utilisez la procédure suivante pour configurer le serveur de l’authentification multifacteur Azure.

1. Ouvrez le serveur de l’authentification multifacteur Azure et cliquez sur l’icône de l’authentification RADIUS. Case à cocher Activer RADIUS d’authentification.
2. Sous l’onglet Clients, vérifiez les ports correspond à ce qui est configuré dans NPS, cliquez sur l’ajout de... bouton. Ajoutez l’adresse IP du serveur passerelle Bureau à distance, nom de l’application (facultatif) et un secret partagé. Le code secret partagé au devront être identique sur le serveur de l’authentification multifacteur Azure et la passerelle Bureau à distance.
3. Cliquez sur l’onglet cible et cliquez sur le bouton d’option ou les serveurs RADIUS.
4. Cliquez sur Ajouter... bouton. Entrez l’adresse IP, secret partagé et les ports du serveur NPS. À moins d’avoir à l’aide d’un serveur NPS central, le client RADIUS et cible RADIUS sera la même. Le secret partagé doit correspondre à la mise en une seule dans la section du client RADIUS du serveur NPS.

![Authentification RADIUS](./media/multi-factor-authentication-get-started-server-rdg/radius.png)
