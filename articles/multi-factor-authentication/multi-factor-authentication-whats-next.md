<properties
    pageTitle="Authentification multifacteur Azure - ensuite ?"
    description="Il s’agit de la page de l’authentification multifacteur Azure qui décrit la marche à suivre avec l’authentification Multifacteur.  Cela inclut les rapports, alerte de fraude, dérivation unique, les messages vocaux personnalisé, la mise en cache, les mots de passe adresses IP et application approuvés."
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
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="kgremban"/>

# <a name="configuring-azure-multi-factor-authentication"></a>Configuration de l’authentification multifacteur Azure

Cet article vous permet de gérer l’authentification multifacteur Azure à présent que vous êtes en cours d’exécution.  Il traite de nombreuses rubriques qui vous aident à tirer le meilleur parti de l’authentification multifacteur Azure.  Toutes ces fonctionnalités sont disponibles dans toutes les versions de l’authentification multifacteur Azure.

La configuration de certaines des fonctionnalités ci-dessous se trouve dans le portail de gestion de l’authentification multifacteur Azure. Il existe deux façons différentes que vous pouvez accéder au portail de gestion de l’authentification Multifacteur, qui sont effectuées via le portail Azure. La première est en gérant un fournisseur d’authentification multifacteur si vous utilisez l’authentification Multifacteur en fonction de la consommation. Le second est via les paramètres de service de l’authentification Multifacteur. La deuxième option nécessite un fournisseur d’authentification multifacteur ou une licence de l’authentification Multifacteur Azure, Azure AD Premium ou Enterprise mobilité Suite.

Pour accéder au portail de gestion de l’authentification Multifacteur via un fournisseur d’authentification multifacteur Azure, se connecter au portail Azure en tant qu’administrateur, puis sélectionnez l’option Active Directory. Cliquez sur l’onglet **Fournisseurs d’authentification multifacteur** , puis sélectionnez votre annuaire et cliquez sur le bouton **Gérer** dans la partie inférieure.

Pour accéder au portail de gestion de l’authentification Multifacteur via la page Paramètres du Service l’authentification Multifacteur, se connecter au portail Azure en tant qu’administrateur, puis sélectionnez l’option Active Directory. Cliquez sur dans votre annuaire, puis sur l’onglet **configurer** . Sous la section l’authentification multifacteur, sélectionnez **Gérer les paramètres de service**. Dans la partie inférieure de la page Paramètres du Service l’authentification Multifacteur, cliquez sur le lien **accédez au portail** .


Fonctionnalité| Description| Ce qui est traité
:------------- | :------------- | :------------- |
[Alerte de fraude](#fraud-alert)|Alerte de fraude peut être configuré et configurez afin que vos utilisateurs peuvent signaler frauduleux tentatives d’accéder à leurs ressources.|Comment configurer, configurer et signaler fraude
[Contournement ponctuel](#one-time-bypass) |Une dérivation ponctuelle permet à un utilisateur s’authentifier une seule fois par « contournement » de l’authentification multifacteur.|Comment installer et configurer une dérivation ponctuelle
[Messages vocaux personnalisé](#custom-voice-messages) |Les messages vocaux personnalisé que vous puissiez utiliser vos propres enregistrements ou le message d’accueil avec l’authentification multifacteur. |Comment installer et configurer des messages et le message d’accueil personnalisé
[La mise en cache](#caching-in-azure-multi-factor-authentication)|La mise en cache vous permet de définir une heure spécifique période afin que les tentatives d’authentification suivantes réussissent automatiquement. |Découvrez comment installer et configurer l’authentification mise en cache.
[Adresses IP autorisées](#trusted-ips)|Adresses IP est une fonctionnalité de l’authentification multifacteur qui permet aux administrateurs d’un client géré ou fédéré ignorer l’authentification multifacteur pour les utilisateurs sont se connecter à partir de l’intranet local de la société de confiance.|Configurer et configurer les adresses IP sont agents pour l’authentification multifacteur
[Application de mots de passe](#app-passwords)|Un mot de passe d’application permet à une application qui n’est pas prenant en charge l’authentification Multifacteur ignorer l’authentification multifacteur et continuer à travailler.|Informations sur les mots de passe application.
[N’oubliez pas de l’authentification multifacteur navigateurs et les périphériques à retenir](#remember-multi-factor-authentication-for-devices-users-trust)|Permet de vous rappeler appareils pour un nombre défini de jours après qu’un utilisateur s’est correctement connecté à l’aide de l’authentification Multifacteur.|Informations sur l’activation de cette fonctionnalité et en configurant le nombre de jours.
[Méthodes de vérification sélectionnable](#selectable-verification-methods)|Vous permet de choisir les méthodes d’authentification disponibles pour les utilisateurs à utiliser.|Informations sur l’activation ou désactivation des méthodes d’authentification spécifiques tels que des messages d’appel ou le texte.



## <a name="fraud-alert"></a>Alerte de fraude
Alerte de fraude peut être configuré et configurez afin que vos utilisateurs peuvent signaler frauduleux tentatives d’accéder à leurs ressources.  Les utilisateurs peuvent signaler fraude ou avec l’application mobile leur téléphone.

### <a name="to-set-up-and-configure-fraud-alert"></a>Pour installer et configurer l’alerte de fraude

1.  Ouvrez une session sur http://azure.microsoft.com
2.  Accédez au portail de gestion de l’authentification Multifacteur conformément aux instructions dans la partie supérieure de cette page.
3.  Dans le portail de gestion Azure de l’authentification multifacteur, cliquez sur paramètres sous la section Configurer.
4.  Sous la section fraude alerte de la page Paramètres, vérifiez l’autoriser les utilisateurs pour envoyer les alertes de fraude case à cocher.
5.  Si vous souhaitez que les utilisateurs à être bloqué lors de la fraude est signalée, placez une coche dans bloquer l’utilisateur lors de la fraude est signalée.
6.  Dans la zone de texte **Code à rapport fraude pendant Initial salutations** , entrez un code qui peut être utilisé lors de la vérification de l’appel. Si un utilisateur entre ce code plus # au lieu de simplement le signe #, une alerte de fraude est signalée.
7.  Dans la partie inférieure, cliquez sur Enregistrer.

>[AZURE.NOTE]
>D’accueil vocaux de Microsoft par défaut demander aux utilisateurs d’appuyer sur 0# pour envoyer une alerte de fraude. Si vous souhaitez utiliser un code différent de 0, vous devez enregistrer et télécharger vos propres messages d’accueil vocaux personnalisé avec des instructions appropriées.


![Cloud](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="to-report-fraud-alert"></a>Alerte de fraude de rapport
Alerte de fraude peut être déclaré deux façons.  Soit via l’application mobile ou téléphone.  

### <a name="to-report-fraud-alert-with-the-mobile-app"></a>Alerte de fraude rapport avec l’application mobile



1. Lorsqu’une vérification est envoyée à votre téléphone, sélectionnez-la pour lancer l’application Microsoft Authenticator.
2. Pour signaler fraude, cliquez sur Annuler et rapport de fraude. Cela affiche une zone s’affiche indiquant que le personnel de Support informatique de votre organisation est communiquée.
3. Cliquez sur fraude de rapport.
4. Dans l’application, cliquez sur Fermer.

![Cloud](./media/multi-factor-authentication-whats-next/report1.png)


![Cloud](./media/multi-factor-authentication-whats-next/fraud2.png)

### <a name="to-report-fraud-alert-with-the-phone"></a>Alerte de fraude rapport avec le téléphone

1. Lors de la vérification appels entrants sur votre téléphone, répondre à celle-ci.  
2. Pour signaler fraude, entrez le code qui a été configuré pour correspondre à la création de rapports fraude via le téléphone, puis sur le signe #. Vous serez informé qu’une alerte de fraude ait été soumise.
3. Mettre fin à l’appel.

### <a name="to-view-the-fraud-report"></a>Pour afficher le rapport de fraude

1. Ouvrez une session sur [http://azure.microsoft.com](https://azure.microsoft.com/)
2. Sur la gauche, sélectionnez Active Directory.
3. Dans la partie supérieure, sélectionnez fournisseurs d’authentification multifacteur. Cela affiche une liste de vos fournisseurs d’authentification multifacteur.
4. Si vous avez plusieurs fournisseurs d’authentification multifacteur, sélectionnez celle que vous souhaitez afficher le rapport alerte fraude et cliquez sur Gérer dans la partie inférieure de la page. Si vous avez une seule, cliquez sur Gérer. Cette action ouvre le portail de gestion de l’authentification multifacteur Azure.
5. Dans le portail de gestion Azure multifacteur authentification, à gauche, sous affichage d’un rapport, cliquez sur l’alerte de fraude.
6. Spécifiez la plage de dates que vous souhaitez afficher dans le rapport. Vous pouvez également spécifier les noms d’utilisateur spécifique, numéros de téléphone et l’état de l’utilisateur.
7. Cliquez sur Exécuter. Cela affiche un rapport similaire à celui ci-dessous. Vous pouvez également cliquer sur Exporter au format CSV si vous souhaitez exporter l’état.

## <a name="one-time-bypass"></a>Contournement ponctuel

Une dérivation ponctuelle permet à un utilisateur s’authentifier une seule fois par « contournement » de l’authentification multifacteur. La dérivation est temporaire et expire après le nombre de secondes spécifié.  Afin que dans les situations où l’application mobile ou téléphone pas reçoit une notification ou un appel téléphonique, vous pouvez activer une dérivation ponctuelle afin que l’utilisateur peut accéder à la ressource souhaitée.

### <a name="to-create-a-one-time-bypass"></a>Pour créer un contournement ponctuel

1.  Ouvrez une session sur http://azure.microsoft.com
2.  Accédez au portail de gestion de l’authentification Multifacteur conformément aux instructions dans la partie supérieure de cette page.
3.  Dans le Azure multifacteur authentification portail de gestion, si vous voyez le nom de votre client ou votre fournisseur de l’authentification Multifacteur Azure gauche avec un + en regard, cliquez sur le + voir différents groupes de réplication de serveur de l’authentification Multifacteur et le groupe Azure par défaut. Cliquez sur le groupe approprié.
4.  Sous Administration des utilisateurs, cliquez sur **Ignorer unique**.
![Cloud](./media/multi-factor-authentication-whats-next/create1.png)
5.  Dans la page dérivation unique, cliquez sur **Nouveau contourner unique**.
6.  Entrez le nom d’utilisateur, le nombre de secondes la dérivation existe, la raison pour la dérivation, puis cliquez sur **Ignorer**.
![Cloud](./media/multi-factor-authentication-whats-next/create2.png)
7.  À ce stade, l’utilisateur doit se connecter avant l’expiration de la dérivation unique.



### <a name="to-view-the-one-time-bypass-report"></a>Pour afficher le rapport dérivation ponctuel

1. Ouvrez une session sur [http://azure.microsoft.com](https://azure.microsoft.com/)
2. Sur la gauche, sélectionnez Active Directory.
3. Dans la partie supérieure, sélectionnez fournisseurs d’authentification multifacteur. Cela affiche une liste de vos fournisseurs d’authentification multifacteur.
4. Si vous avez plusieurs fournisseurs d’authentification multifacteur, sélectionnez celle que vous souhaitez afficher le rapport alerte fraude et cliquez sur Gérer dans la partie inférieure de la page. Si vous avez une seule, cliquez sur Gérer. Cette action ouvre le portail de gestion de l’authentification multifacteur Azure.
5. Dans le portail de gestion Azure multifacteur authentification, à gauche, sous affichage d’un rapport, cliquez sur Ignorer unique.
6. Spécifiez la plage de dates que vous souhaitez afficher dans le rapport. Vous pouvez également spécifier les noms d’utilisateur spécifique, numéros de téléphone et l’état de l’utilisateur.
7. Cliquez sur Exécuter. Cela affiche un rapport similaire à celui ci-dessous. Vous pouvez également cliquer sur Exporter au format CSV si vous souhaitez exporter l’état.

<center>![Cloud](./media/multi-factor-authentication-whats-next/report.png)</center>

## <a name="custom-voice-messages"></a>Messages vocaux personnalisé

Les messages vocaux personnalisé que vous puissiez utiliser vos propres enregistrements ou le message d’accueil avec l’authentification multifacteur.  Ceux-ci peuvent être utilisés en plus d’ou pour remplacer le Microsoft les enregistrements.

Avant de commencer tenir compte des éléments suivants :

- Les formats de fichier pris en charge en cours sont .wav et MP3.
- La limite de taille de fichier est 5 Mo.
- Il est recommandé de pour l’authentification des messages qui en faire pas comporter plus de 20 secondes. Toute valeur supérieure à ceci peut entraîner la vérification échouera parce que l’utilisateur peut ne pas répond avant que le message se termine et la vérification arrive à expiration.



### <a name="to-set-up-custom-voice-messages-in-azure-multi-factor-authentication"></a>Pour configurer les messages vocaux personnalisée dans l’authentification multifacteur Azure
1.  Créer un message vocal personnalisé à l’aide d’un des formats de fichier pris en charge.
2.  Ouvrez une session sur http://azure.microsoft.com
3.  Accédez au portail de gestion de l’authentification Multifacteur conformément aux instructions dans la partie supérieure de cette page.
4.  Dans le portail de gestion Azure de l’authentification multifacteur, cliquez sur les Messages vocaux sous la section Configurer.
5.  Sous la section de Messages vocaux, cliquez sur **Nouveau Message vocal**.
![Cloud](./media/multi-factor-authentication-whats-next/custom1.png)
6.  Sur la configurer : les Messages vocaux nouvelle page, cliquez sur **Gérer les fichiers audio**.
![Cloud](./media/multi-factor-authentication-whats-next/custom2.png)
7.  Sur la configurer : page fichiers de son, cliquez sur **Télécharger un fichier son**.
![Cloud](./media/multi-factor-authentication-whats-next/custom3.png)
8.  Sur la configurer : télécharger le fichier audio, cliquez sur **Parcourir** et accédez à votre message vocal, cliquez sur **Ouvrir**.
![Cloud](./media/multi-factor-authentication-whats-next/custom4.png)
9.  Ajouter une Description et cliquez sur Télécharger.
10. Une fois que cette opération terminée, un message confirme que vous avez correctement téléchargé le fichier.
11. Sur la gauche, cliquez sur les Messages vocaux.
12. Sous la section de Messages vocaux, cliquez sur Nouveau Message vocal.
13. Dans le menu déroulant Langue, sélectionnez une langue.
14. Si ce message pour une application spécifique, spécifiez dans la zone de l’Application.
15. À partir du Type de Message, sélectionnez le type de message à être remplacés par notre nouveau message personnalisé.
16. Dans le menu déroulant fichier audio, sélectionnez votre fichier audio.
17. Cliquez sur **créer**. Un message confirme que vous avez créé un message vocal.
![Cloud](./media/multi-factor-authentication-whats-next/custom5.png)</center>



## <a name="caching-in-azure-multi-factor-authentication"></a>La mise en cache dans l’authentification multifacteur Azure

La mise en cache vous permet de définir une heure spécifique période afin que les tentatives d’authentification suivantes réussissent automatiquement. Cette opération concerne principalement lorsque systèmes locaux tels que VPN envoient plusieurs demandes de vérification alors que la première requête est toujours en cours. Ainsi, les demandes suivantes réussir automatiquement une fois que l’utilisateur a réussi la vérification en cours. Notez que la mise en cache n’est pas destiné à être utilisé pour les connexions à Azure Active Directory.


### <a name="to-set-up-caching-in-azure-multi-factor-authentication"></a>Pour configurer la mise en cache dans l’authentification multifacteur Azure

1.  Ouvrez une session sur http://azure.microsoft.com
2.  Accédez au portail de gestion de l’authentification Multifacteur conformément aux instructions dans la partie supérieure de cette page.
3.  Dans le portail de gestion Azure de l’authentification multifacteur, cliquez sur mise en cache sous la section Configurer.
4.  Configurer la mise en cache de page en cliquant sur Nouveau Cache
5.  Sélectionnez le type de Cache et les secondes du cache. Cliquez sur Créer.

<center>![Cloud](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>Adresses IP autorisées

Adresses IP est une fonctionnalité de l’authentification multifacteur qui permet aux administrateurs d’un client géré ou fédéré ignorer l’authentification multifacteur pour les utilisateurs sont se connecter à partir de l’intranet local de la société de confiance. Les fonctionnalités sont disponibles pour les clients Azure AD qui ont des licences Azure AD Premium, entreprise mobilité Suite ou l’authentification multifacteur Azure.


Type de client AD Azure| Options IP autorisées disponibles
:------------- | :------------- |
Gérés|Plages d’adresses IP spécifiques – les administrateurs peuvent spécifier une plage d’adresses IP pouvant contourner l’authentification multifacteur pour les utilisateurs sont se connecter à partir de l’intranet de la société.
Fédéré|<li>Tous les utilisateurs fédérés : tous les utilisateurs fédérés qui sont la signature à l’aide de l’organisation passant l’authentification multifacteur à l’aide d’une demande de remboursement émis par AD FS.</li><li>Plages d’adresses IP spécifiques – les administrateurs peuvent spécifier une plage d’adresses IP pouvant contourner l’authentification multifacteur pour les utilisateurs sont se connecter à partir de l’intranet de la société.

Cela contourner fonctionne uniquement à partir d’intranet d’une société. Par exemple, si vous n’avez sélectionné tous les utilisateurs fédérés, et un utilisateur se connecte à partir d’en dehors de l’intranet de la société, cet utilisateur doit s’authentifier à l’aide de l’authentification multifacteur même si l’utilisateur présente un réclamer AD FS. Le tableau suivant décrit lorsque plusieurs facteurs d’authentification et d’application les mots de passe sont requises à l’intérieur de votre réseau d’entreprise et en dehors de votre réseau d’entreprise lorsque approuvés adresses IP est activée.


|Adresses IP activé autorisées| Adresses IP désactivé autorisées
:------------- | :------------- | :------------- |
Réseau d’entreprise à l’intérieur|Pour les flux de navigateur, l’authentification multifacteur pas obligatoire.|Pour les flux de navigateur, l’authentification multifacteur obligatoire
|Pour les applications clientes enrichies, les mots de passe régulières fonctionnent si l’utilisateur n’a pas créé les mots de passe d’application. Après avoir créé un mot de passe d’application, les mots de passe application est requis.|Pour les applications clientes enrichies, les mots de passe application obligatoire
Réseau d’entreprise externe|Pour les flux de navigateur, l’authentification multifacteur obligatoire.|Pour les flux de navigateur, l’authentification multifacteur obligatoire.
|Pour les applications clientes enrichies, les mots de passe application obligatoire.|Pour les applications clientes enrichies, les mots de passe application obligatoire.

### <a name="to-enable-trusted-ips"></a>Pour activer les adresses IP approuvés

1. Connectez-vous au portail classique Azure.
2. Sur la gauche, cliquez sur Active Directory.
3. Sous, répertoire cliquez sur le répertoire que vous souhaitez configurer IPsing approuvés sur.
4. Dans le répertoire que vous avez sélectionné, cliquez sur Configurer.
5. Dans la section authentification multifacteur, cliquez sur Gérer les paramètres de service.
6. Dans la page Paramètres du Service, sous adresses IP approuvés, sélectionnez des options :

    - Pour les demandes de fédérées utilisateurs provenant de mon intranet – tout fédérées utilisateurs sont se connecter à partir du réseau d’entreprise permet d’ignorer l’authentification multifacteur à l’aide d’une demande de remboursement émis par AD FS.
    - Pour les requêtes à partir d’une plage spécifique d’adresses IP public – Entrez les adresses IP dans les zones fournies à l’aide de la notation CIDR. Par exemple : xxx.xxx.xxx.0/24 d’adresses IP dans la plage xxx.xxx.xxx.1 – xxx.xxx.xxx.254 ou xxx.xxx.xxx.xxx/32 pour une seule adresse IP. Vous pouvez entrer jusqu'à 50 plages d’adresses IP.

7. Cliquez sur Enregistrer.
8. Une fois que les mises à jour ont été appliquées, cliquez sur Fermer.



![Adresses IP autorisées](./media/multi-factor-authentication-whats-next/trustedips3.png)




## <a name="app-passwords"></a>Application de mots de passe

Dans certaines applications, telle que Office 2010 ou version antérieure et Apple Mail, vous ne pouvez pas utiliser l’authentification multifacteur.  Pour utiliser ces applications, vous devez utiliser « application les mots de passe » à la place de votre mot de passe traditionnel.  Le mot de passe d’application permet à l’application ignorer l’authentification multifacteur et continuer à travailler.

>[AZURE.NOTE] Authentification moderne pour les Clients Office 2013
>
> Les clients Office 2013 (y compris Outlook) prend en charge de nouveaux protocoles d’authentification maintenant et peuvent être activées pour prendre en charge l’authentification multifacteur.  Cela signifie qu’une fois activée, les mots de passe application ne sont pas obligatoires pour une utilisation avec les clients Office 2013.  Pour plus d’informations, voir [présentation publique de l’authentification moderne Office 2013 annoncée](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).



### <a name="important-things-to-know-about-app-passwords"></a>Points importants à connaître les mots de passe application

Voici une liste des choses que vous devez savoir sur les mots de passe application importantes.

- Les utilisateurs peuvent avoir plusieurs application les mots de passe, qui permet d’augmenter la surface de vol. Les mots de passe application étant difficiles à mémoriser, il peut Encouragez-les à noter ce qui suit. Ce n’est pas recommandé et doit être déconseillé, car seul facteur est requis pour vous connecter avec un mot de passe de l’application.
- Applications qui mettent en cache les mots de passe et l’utiliser dans des scénarios en local peuvent démarrer ignore car le mot de passe d’application n’est pas connue en dehors de l’id d’organisation. Par exemple, des messages électroniques Exchange qui sont en local, mais les messages archivés sont dans le cloud. Le même mot de passe ne fonctionne pas.
- Le mot de passe est généré automatiquement et n’est pas fourni par l’utilisateur. C’est parce que le mot de passe généré automatiquement, est plus difficile pour un pirate à deviner et n’est plus sécurisé.
- Il existe actuellement une limite de 40 mots de passe par utilisateur. Vous devrez supprimer un de vos mots de passe d’application existant afin de créer un nouvel identifiant.
- Une fois que l’authentification multifacteur est activée sur un compte d’utilisateur, les mots de passe application peuvent être utilisés avec la plupart des autres que des navigateurs clients tels que Outlook et Lync, mais les actions d’administration ne peut pas être effectuées à l’aide de mots de passe application grâce à des applications autres que des navigateurs tels que Windows PowerShell même si cet utilisateur possède un compte d’administrateur.  Vérifiez que vous créez un compte de service avec un mot de passe fort pour exécuter des scripts PowerShell et que vous n’activez pas ce compte pour l’authentification multifacteur.

>[AZURE.WARNING]  Les mots de passe application ne fonctionnent pas dans les environnements hybrides où les clients communiquent avec les deux en local et nuage de points de terminaison de découverte automatique. C’est parce que les mots de passe domaine sont requises pour l’authentification en local et application de mots de passe est nécessaires pour vous authentifier avec le cloud.


### <a name="naming-guidance-for-app-passwords"></a>Conseils d’appellation pour les mots de passe application
Il est recommandé que les noms de mot de passe d’application doivent refléter l’appareil sur lequel elles sont utilisées. Par exemple, si vous possédez un ordinateur portable qui comporte des applications autres que des navigateurs tels que Outlook, Word et Excel, vous devez uniquement créer un mot de passe de l’application nommé portable et utiliser ce mot de passe d’application dans l’ensemble de ces applications. Bien que vous pouvez créer des mots de passe séparés pour l’ensemble de ces applications, il n’est pas recommandé. La recommandation méthode consiste à utiliser un mot de passe application par périphérique.


<center>![Cloud](./media/multi-factor-authentication-whats-next/naming.png)</center>


### <a name="federated-sso-app-passwords"></a>Mots de passe application fédéré (SSO)
Azure AD prend en charge la fédération avec local Windows Server Active Directory Domain Services (AD DS). Si votre organisation est federated(SSO) avec Azure Active Directory et que vous vous apprêtez à utiliser l’authentification multifacteur Azure, puis Voici des informations importantes que vous prenez en considération lors de l’utilisation de mots de passe de l’application. Cela s’applique uniquement aux clients federated(SSO).

- Le mot de passe d’application vérifié par Azure Active Directory et par conséquent ignore la fédération. Fédération est uniquement connue lors de la configuration d’application de mot de passe.
- Pour les utilisateurs federated(SSO), nous atteindre jamais le fournisseur d’identité (IdP) à la différence du flux passif. Les mots de passe sont stockés dans l’id d’organisation. Si l’utilisateur quitte l’entreprise, ces informations doit se déplace vers id d’organisation à l’aide de la synchronisation d’annuaire en temps réel. Désactiver/suppression du compte peut prendre jusqu'à trois heures pour synchroniser, retarder désactiver/suppression du mot de passe de l’application dans Azure Active Directory.
- Paramètres de contrôle d’accès Client local ne sont pas respectés par mot de passe de l’application
- Aucune authentification locaux journalisation / capacité d’audit n’est disponible pour votre mot de passe application
- Plus éducation de l’utilisateur final est requise pour le client Microsoft Lync 2013. Pour les étapes à suivre, voir comment faire pour modifier le mot de passe dans votre message électronique pour le mot de passe d’application.
- Certains modèles architecturales avancées peuvent nécessiter l’utilisation d’une combinaison de nom d’utilisateur d’organisation et les mots de passe et les mots de passe application lors de l’utilisation de l’authentification multifacteur avec des clients, selon l’endroit où ils s’authentifier. Pour les clients qui s’authentifier une infrastructure localement, vous utiliserez une organisation nom d’utilisateur et mot de passe. Pour les clients qui s’authentifier Azure AD, vous devez utiliser le mot de passe d’application.

Par exemple, supposons que vous avez une architecture qui comprend les éléments suivants :

- Vous utilisez la fédération votre instance locales d’Active Directory avec Azure Active Directory
- Vous utilisez Exchange online
- Vous utilisez Lync est spécifiquement locales
- Vous utilisez l’authentification multifacteur Azure


![Proofup](./media/multi-factor-authentication-whats-next/federated.png)

 Dans ce cas, vous devez procédez comme suit :

- Quand se connecter à Lync, utilisez votre nom d’utilisateur et mot de passe.
- Lorsque vous tentez d’accéder au carnet d’adresses via un client Outlook qui se connecte à Exchange online, utilisez un mot de passe d’application.

### <a name="allowing-app-password-creation"></a>Autorisation de création de mot de passe d’application
Par défaut, les utilisateurs ne peuvent pas créer des mots de passe de l’application.  Cette fonctionnalité doit être activée.  Pour permettre aux utilisateurs la possibilité de créer des mots de passe de l’application, procédez comme suit.

#### <a name="to-enable-users-to-create-app-passwords"></a>Pour permettre aux utilisateurs de créer des mots de passe de l’application



1. Connectez-vous au portail classique Azure.
2. Sur la gauche, cliquez sur Active Directory.
3. Sous, répertoire cliquez sur l’annuaire de l’utilisateur que vous souhaitez activer.
4. Dans la partie supérieure, cliquez sur utilisateurs.
5. Dans la partie inférieure de la page, cliquez sur Gérer les authentification multifacteur  
6. En haut de la page de l’authentification multifacteur, cliquez sur Paramètres du Service.
7. Vérifiez que la case en regard d’Autoriser les utilisateurs à créer l’application de mots de passe pour vous connecter à des applications de navigateur non est sélectionnée.


![Créer des mots de passe de l’application](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="creating-app-passwords"></a>Création de mots de passe de l’application
Les utilisateurs peuvent créer des mots de passe application lors de leur enregistrement initial.  Ils ont la possibilité à la fin du processus d’enregistrement qui leur permet de les créer.

En outre les utilisateurs peuvent également créer les mots de passe application ultérieurement en modifiant leurs paramètres dans le portail Azure, le portail Office 365 ou en

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Pour créer des mots de passe de l’application dans le portail Office 365
--------------------------------------------------------------------------------


1. Connectez-vous au portail Office 365
2. Dans le coin supérieur droit, sélectionnez le widget de paramètres
3. Sur la gauche, sélectionnez la vérification de sécurité supplémentaires
4. Sur la droite, sélectionnez **mettre à jour mes numéros de téléphone utilisé pour la sécurité du compte**
5. Dans la page proofup, dans la partie supérieure, sélectionnez les mots de passe application
6. Cliquez sur **créer**
7. Entrez un nom pour le mot de passe d’application et cliquez sur **suivant**
8. Copier le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.

<center>![Cloud](./media/multi-factor-authentication-whats-next/security.png)</center>


### <a name="to-create-app-passwords-in-the-azure-portal"></a>Pour créer des mots de passe de l’application dans le portail Azure
--------------------------------------------------------------------------------
1. Connectez-vous au portail classique Azure.
3. Dans la partie supérieure, avec le bouton droit sur votre nom d’utilisateur, puis sélectionnez Vérification de sécurité supplémentaire.
5. Dans la page proofup, dans la partie supérieure, sélectionnez les mots de passe application
6. Cliquez sur **créer**
7. Entrez un nom pour le mot de passe d’application et cliquez sur **suivant**
8. Copier le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.


![Mots de passe application](./media/multi-factor-authentication-whats-next/app2.png)

### <a name="to-create-app-passwords-if-you-do-not-have-an-office-365-or-azure-subscription"></a>Pour créer des mots de passe de l’application si vous ne disposez pas d’un abonnement Office 365 ou Azure
--------------------------------------------------------------------------------
1. Se connecter à [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Dans la partie supérieure, sélectionnez profil.
3. Cliquez sur votre nom d’utilisateur, sélectionnez la vérification de sécurité supplémentaires.
5. Dans la page proofup, dans la partie supérieure, sélectionnez les mots de passe application
6. Cliquez sur **créer**
7. Entrez un nom pour le mot de passe d’application et cliquez sur **suivant**
8. Copier le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.

![Mots de passe application](./media/multi-factor-authentication-whats-next/myapp.png)

## <a name="remember-multi-factor-authentication-for-devices-users-trust"></a>N’oubliez pas de l’authentification multifacteur pour appareils utilisateurs approbation

Mémoriser l’authentification multifacteur pour les appareils et navigateurs que la confidentialité des utilisateurs est une fonctionnalité gratuite pour tous les utilisateurs de l’authentification Multifacteur.  Il permet de donner aux utilisateurs la possibilité pour l’authentification Multifacteur dérivation pendant un nombre défini de jours après l’exécution d’une connexion à l’aide de l’authentification Multifacteur. Ceci permet d’améliorer la facilité d’utilisation pour vos utilisateurs.

Toutefois, étant donné que les utilisateurs sont autorisés à mémoriser l’authentification Multifacteur pour périphériques approuvés, cette fonctionnalité peut affecter la sécurité de compte. Pour vous assurer que la sécurité du compte, vous devez restaurer l’authentification multifacteur pour leurs appareils mobiles pour une des situations suivantes :

- Si leur compte d’entreprise est plus sécurisé
- Si un appareil à retenir perte ou de vol

> [AZURE.NOTE] Cette fonctionnalité est activée comme un cache de cookies de navigateur. Il ne fonctionne pas si votre navigateur, les cookies ne sont pas activés.

### <a name="how-to-enabledisable-remember-multi-factor-authentication"></a>Comment faire pour activer/désactiver l’authentification multifacteur mémoriser

1. Connectez-vous au portail classique Azure.
2. Sur la gauche, cliquez sur Active Directory.
3. Sous Active Directory, cliquez sur le répertoire que vous souhaitez configurer n’oubliez pas de l’authentification multifacteur pour appareils mobiles.
4. Dans le répertoire que vous avez sélectionné, cliquez sur Configurer.
5. Dans la section authentification multifacteur, cliquez sur Gérer les paramètres de service.
6. Dans la page Paramètres du Service, sous Gérer les paramètres du périphérique utilisateur, sélectionner/désélectionner les **Autoriser les utilisateurs de se rappeler l’authentification multifacteur sur les appareils qu’ils approuvent**.
![N’oubliez pas de périphériques](./media/multi-factor-authentication-whats-next/remember.png)
8. Définissez le nombre de jours pendant lequel vous souhaitez autoriser la suspension. La valeur par défaut est 14 jours.
9. Cliquez sur Enregistrer.
10. Cliquez sur Fermer.


## <a name="selectable-verification-methods"></a>Méthodes de vérification sélectionnable
Les versions à la fois le cloud et en local, vous pouvez choisir les méthodes de vérification disponibles pour vos utilisateurs. Le tableau suivant fournit un bref aperçu de chaque méthode.

Lorsque vos utilisateurs s’inscrire leurs comptes pour l’authentification Multifacteur, ils choisissent sa méthode de vérification préféré déconnecter les options que vous avez activé. Les instructions pour leur processus d’inscription sont couvert dans [configurer mon compte pour la vérification](multi-factor-authentication-end-user-first-time.md)

Méthode|Description
:------------- | :------------- |
Appel vers téléphone |  Passer un appel vocal automatisé sur le téléphone de l’authentification. L’utilisateur répond à l’appel et appuie sur # dans le pavé de téléphone s’authentifier. Ce numéro de téléphone n’est pas synchronisé avec Active Directory local.
Message texte vers téléphone | Envoie un message texte contenant un code de vérification à l’utilisateur. L’utilisateur est invité à soit réponse au message texte avec le code de vérification ou pour entrer le code de vérification dans l’interface de connexion.
Notification via l’application mobile | Dans ce mode, l’application Microsoft Authenticator empêche l’accès non autorisé aux comptes et arrête transactions frauduleuses. Pour cela, à l’aide d’une notification push pour votre téléphone ou appareil enregistré. Simplement afficher la notification et s’il s’agit légitime vous appuyez sur vérifier. Dans le cas contraire, vous pouvez sélectionnez Refuser ou sélectionnez Refuser et signaler la notification frauduleuse. Pour plus d’informations sur la déclaration des notifications frauduleuses apprendre à utiliser la fonctionnalité de fraude de rapport et refuser pour l’authentification multifacteur.</br></br>L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).|
Code de vérification à partir de l’application mobile | Dans ce mode, l’application Microsoft Authenticator utilisable comme un jeton logiciel pour générer un code de vérification de formule. Ce code de vérification peut ensuite être entré ainsi que le nom d’utilisateur et mot de passe pour fournir la deuxième forme d’authentification.</li><br><p> L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

### <a name="how-to-enabledisable-authentication-methods"></a>Comment faire pour activer/désactiver les méthodes d’authentification

1. Connectez-vous au portail classique Azure.
2. Sur la gauche, cliquez sur Active Directory.
3. Sous Active Directory, cliquez sur le répertoire que vous souhaitez activer ou désactiver les méthodes d’authentification.
4. Dans le répertoire que vous avez sélectionné, cliquez sur Configurer.
5. Dans la section authentification multifacteur, cliquez sur Gérer les paramètres de service.
6. Dans la page Paramètres du Service, sous options de vérification, sélectionner/désélectionner les options que vous souhaitez utiliser.</br></br>
![Options de vérification](./media/multi-factor-authentication-whats-next/authmethods.png)
9. Cliquez sur Enregistrer.
10. Cliquez sur Fermer.
