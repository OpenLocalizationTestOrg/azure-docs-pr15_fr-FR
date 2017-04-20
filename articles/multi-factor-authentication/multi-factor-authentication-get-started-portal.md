<properties 
    pageTitle="Déployez le portail d’utilisateur pour le serveur de l’authentification multifacteur Azure"
    description="Il s’agit de la page de l’authentification multifacteur Azure qui explique comment commencer à utiliser l’authentification Multifacteur Azure et le portail d’utilisateur."
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

# <a name="deploying-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Déployez le portail d’utilisateur pour le serveur de l’authentification multifacteur Azure

Le portail d’utilisateur permet à l’administrateur installer et configurer le portail d’utilisateur d’authentification multifacteur Azure. Le portail d’utilisateur est un site web IIS ce qui permet aux utilisateurs de s’inscrire à l’authentification multifacteur Azure et mettre à jour leurs comptes. Un utilisateur peut modifier son numéro de téléphone, modifier son code confidentiel ou ignorer l’authentification multifacteur Azure au cours de leur prochaine ouverture de session sur.

Les utilisateurs se connecter au portail utilisateur à l’aide de leur nom d’utilisateur normal et le mot de passe et sera soit effectuer un appel de l’authentification multifacteur Azure ou répondre à des questions de sécurité pour terminer leur authentification. Si l’inscription d’un utilisateur est autorisée, un utilisateur configure son numéro de téléphone et le code confidentiel de la première fois qu’ils se connecter au portail utilisateur.

Utilisateur portail les administrateurs peuvent configurer et autorisation permettant d’ajouter de nouveaux utilisateurs et mettre à jour les utilisateurs existants.

<center>![Programme d’installation](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploying-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Déploiement du portail utilisateur sur le même serveur que le serveur de l’authentification multifacteur Azure

Les composants requis suivants sont requises pour installer le portail utilisateurs du même serveur que le serveur de l’authentification multifacteur Azure :

- IIS doit être installé, y compris asp.net et compatibilité base meta IIS 6 (pour IIS 7 ou version ultérieure)
- Connecté utilisateur doit disposer de droits d’administrateur de l’ordinateur et le domaine le cas échéant.  C’est parce que le compte a besoin d’autorisations pour créer des groupes de sécurité Active Directory.

### <a name="to-deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Pour déployer le portail d’utilisateur pour le serveur de l’authentification multifacteur Azure

1. Au sein du serveur de l’authentification multifacteur Azure : cliquez sur User Portal icône dans le menu de gauche, cliquez sur le bouton Installer User Portal.
1. Cliquez sur Suivant.
1. Cliquez sur Suivant.
1. Si l’ordinateur est joint à un domaine et la configuration d’Active Directory pour sécuriser les communications entre le portail d’utilisateur et le service d’authentification multifacteur Azure est incomplète, l’étape Active Directory s’affichera. Cliquez sur le bouton suivant pour compléter automatiquement cette configuration.
1. Cliquez sur Suivant.
1. Cliquez sur Suivant.
1. Cliquez sur Fermer.
1. Ouvrez un navigateur web à partir de n’importe quel ordinateur et accédez à l’URL où le portail de l’utilisateur a été installé (par exemple, https://www.publicwebsite.com/MultiFactorAuth). Vérifiez qu’aucun certificat avertissements ou des erreurs ne sont affichés.

<center>![Programme d’installation](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploying-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Déployer le portail d’utilisateur l’authentification multifacteur Azure Server sur un serveur différent

Pour pouvoir utiliser l’application de l’authentification multifacteur Azure, les éléments suivants sont nécessaires pour que l’application peut communiquer avec le portail de l’utilisateur :

Consultez matérielle et logicielle requise pour la configuration matérielle et logicielle :

- Vous devez utiliser 6.0 ou version ultérieure du serveur de l’authentification multifacteur Azure.
- Portail de l’utilisateur doit être installé sur un serveur web via Internet exécutant Microsoft® Internet Information Services (IIS) 6.x, IIS 7.x ou version ultérieure.
- Lorsque vous utilisez IIS 6.x, vérifiez ASP.NET v2.0.50727 est installé, enregistré et affectez la valeur autorisé.
- Obligatoire les services de rôle lors de l’utilisation de IIS 7.x ou version ultérieure contient ASP.NET et compatibilité avec la métabase IIS 6.
- Portail de l’utilisateur doit être sécurisé avec un certificat SSL.
- Azure multifacteur authentification Web Service SDK doit être installé dans IIS 6.x, IIS 7.x ou version ultérieure sur le serveur installée sur le serveur de l’authentification multifacteur Azure.
- Azure multifacteur authentification Web Service SDK doivent être sécurisé avec un certificat SSL.
- Portail de l’utilisateur doit pouvoir vous connecter à l’authentification Azure multifacteur Web Service SDK sur SSL.
- Portail de l’utilisateur doit pouvoir s’authentifier à l’Azure multifacteur authentification SDK du Service Web utilisant les informations d’identification d’un compte de service qui fait partie d’un groupe de sécurité nommé « PhoneFactor administrateurs ». Ce compte de service et le groupe existent dans Active Directory si le serveur de l’authentification multifacteur Azure s’exécute sur un serveur à un domaine. Ce compte de service et le groupe existent localement sur le serveur de l’authentification multifacteur Azure s’il n’est pas joint à un domaine.

Installez le portail d’utilisateur sur un serveur autre que le serveur de l’authentification multifacteur Azure requiert les trois étapes suivantes :

1. Installez le Kit de développement de service web
2. Installer le portail d’utilisateur
3. Configurer les paramètres du portail utilisateur sur le serveur de l’authentification multifacteur Azure


### <a name="install-the-web-service-sdk"></a>Installez le Kit de développement de service web

Si le Service Web Azure multifacteur authentification SDK n’est pas déjà installé sur le serveur de l’authentification multifacteur Azure, accédez à ce serveur et ouvrez le serveur de l’authentification multifacteur Azure. Cliquez sur l’icône SDK du Service Web, cliquez sur le SDK du Service Web installer... bouton et suivez les instructions affichées. Le Kit de développement de Service Web doit être sécurisé avec un certificat SSL. Un certificat auto-signé est OK à cet effet, mais il doit être importés dans le magasin « Approuvés autorités de Certification racine » du compte d’ordinateur Local sur le serveur web portail utilisateur afin qu’il approuve ce certificat lors de l’initialisation de la connexion SSL.

<center>![Programme d’installation](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="install-the-user-portal"></a>Installer le portail d’utilisateur

Avant d’installer le portail d’utilisateur sur un serveur différent, gardez les éléments suivants :

- Il est utile ouvrir un navigateur web sur le serveur web via Internet et accédez à l’URL du Kit de développement de Service Web qui ont été entrées dans le fichier web.config. Si le navigateur peut accéder au service web avec succès, elle doit invite à entrer les informations d’identification. Entrez le nom d’utilisateur et mot de passe qui ont été entrées dans le fichier web.config exactement tel qu’il apparaît dans le fichier. Vérifiez qu’aucun certificat avertissements ou des erreurs ne sont affichés.
- Si un proxy inverse ou un pare-feu se trouve devant le serveur web utilisateur portail et effectuez décharger SSL, vous pouvez modifier le fichier web.config User Portal et ajouter la clé suivante pour le <appSettings> section afin que le portail d’utilisateur utilisent http au lieu de https. <add key="SSL_REQUIRED" value="false"/>

#### <a name="to-install-the-user-portal"></a>Pour installer le portail d’utilisateur

1. Ouvrez l’Explorateur Windows sur le serveur de l’authentification multifacteur Azure server et accédez au dossier où le serveur de l’authentification multifacteur Azure est installé (par exemple, C:\Program Files\Multi facteur d’authentification serveur). Choisir la version 32 bits ou 64 bits du fichier d’installation MultiFactorAuthenticationUserPortalSetup selon le cas pour le serveur qui User Portal seront installé sur. Copiez le fichier d’installation sur le serveur via Internet.
2. Sur le serveur web via Internet, le fichier d’installation doit être exécuté avec des droits d’administrateur. Le plus simple consiste à ouvrir une invite de commandes en tant qu’administrateur et accédez à l’emplacement où le fichier d’installation a été copié.
3. Exécutez le programme d’installation MultiFactorAuthenticationUserPortalSetup64, modifiez le nom du Site et répertoire virtuel si vous le souhaitez.
4. Après avoir terminé l’installation du portail utilisateur, accédez à C:\inetpub\wwwroot\MultiFactorAuth (ou répertoire approprié basé sur le nom du répertoire virtuel), puis modifiez le fichier web.config.
5. Recherchez la clé USE_WEB_SERVICE_SDK et changez la valeur false en true. Recherchez les clés WEB_SERVICE_SDK_AUTHENTICATION_USERNAME et WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD et définissez les valeurs pour le nom d’utilisateur et mot de passe du compte de service qui est un membre de la sécurité PhoneFactor Admins groupe (voir la section ci-dessus). Veillez à entrer le nom d’utilisateur et mot de passe entre les guillemets à la fin de la ligne (valeur = » « / >). Il est recommandé d’utiliser un nom d’utilisateur complet (par exemple, DOMAINE\nom d’utilisateur ou ordinateur om d’utilisateur)
6. Recherchez le paramètre pfup_pfwssdk_PfWsSdk et définissez la valeur de « http://localhost:4898/PfWsSdk.asmx » à l’URL du Kit de développement de Service Web qui s’exécute sur le serveur de l’authentification multifacteur Azure (par exemple, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Étant donné que SSL est utilisé pour cette connexion, vous devez faire référence le Kit de développement de Service Web par le nom du serveur et non adresse IP étant donné que le certificat SSL ne sera ont été émis pour le nom du serveur et l’URL utilisée doit correspondre au nom sur le certificat. Si le nom du serveur ne résout pas à une adresse IP du serveur via Internet, ajoutez une entrée au fichier hosts sur ce serveur pour établir une correspondance entre le nom du serveur de l’authentification multifacteur Azure son adresse IP. Enregistrez le fichier web.config après que modifications ont été apportées.
7. Si le site Web que portail de l’utilisateur a été installé sous (par exemple, Site Web par défaut) n’a pas encore été relié avec un certificat signé publiquement, installez le certificat sur le serveur si ce n’est pas déjà installé, ouvrez le Gestionnaire IIS et lier le certificat au site Web.
8. Ouvrez un navigateur web à partir de n’importe quel ordinateur et accédez à l’URL où le portail de l’utilisateur a été installé (par exemple, https://www.publicwebsite.com/MultiFactorAuth). Vérifiez qu’aucun certificat avertissements ou des erreurs ne sont affichés.



## <a name="configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Configurer les paramètres du portail utilisateur sur le serveur de l’authentification multifacteur Azure
Maintenant que le portail est installé, vous devez configurer le serveur de l’authentification multifacteur Azure pour l’utiliser avec le portail.

Serveur de l’authentification multifacteur Azure offre plusieurs options pour le portail d’utilisateur.  Le tableau suivant fournit une liste de ces options et obtenir une explication de leur utilisation.

Paramètres du portail utilisateur|Description|
:------------- | :------------- |
URL du portail utilisateur| Vous permet d’entrer l’URL de l’endroit où le portail est hébergé.
Authentification principale| Vous permet de spécifier le type d’authentification à utiliser pour vous connecter au portail.  Authentification Windows, Radius ou LDAP.
Permettre aux utilisateurs de se connecter|Permet aux utilisateurs d’entrer un nom d’utilisateur et mot de passe dans la page de connexion pour le portail d’utilisateur.  Si elle n’est pas activée, les zones sont grisés.
Autoriser l’inscription de l’utilisateur|Permet à l’utilisateur à s’inscrire à l’authentification multifacteur en les mettant à un écran de configuration qui invite à entrer les informations supplémentaires telles que le numéro de téléphone.  Invite pour téléphone sauvegarde permet aux utilisateurs de spécifier un numéro de téléphone secondaire.  Choisir le tiers jeton de formule permet aux utilisateurs de spécifier un jeton de formule 3e partie.
Autoriser les utilisateurs à démarrer une conversation unique dérivation| Cela permet aux utilisateurs de lancer une dérivation unique.  Si un ensembles d’utilisateurs cette installation il faudra affectent la prochaine fois que l’utilisateur se connecte.  Choisir le contournement secondes fournit à une zone de l’utilisateur afin qu’ils peuvent modifier la valeur par défaut de 300 secondes.  Dans le cas contraire, la dérivation ponctuelle n’est adaptée à 300 secondes.
Permettre aux utilisateurs de sélectionner la méthode| Permet aux utilisateurs de spécifier leur méthode de contact principale.  Cela peut être un appel téléphonique, message texte, l’application mobile ou jeton de formule.
Permettre aux utilisateurs de sélectionner la langue|  Permet à l’utilisateur changer la langue utilisée pour l’appel téléphonique, message texte, l’application mobile ou jeton de formule.
Autoriser les utilisateurs à activer l’application mobile| Permet aux utilisateurs de générer un code d’activation pour terminer le processus d’activation de l’application mobile qui est utilisé avec le serveur.  Vous pouvez également définir le nombre d’unités qu’ils peuvent activer cette sur.  Entre 1 et 10.
Utiliser des questions sur la sécurité de secours|Vous permet d’utiliser les questions de sécurité en cas d’échec de l’authentification multifacteur.  Vous pouvez spécifier le nombre de questions de sécurité qui doivent être traitées avec succès.
Autoriser les utilisateurs à associer jeton de formule tiers| Permet aux utilisateurs de spécifier un jeton de formule tiers.
Utilisation de jeton de formule de secours|Permet l’utilisation d’un jeton de formule dans le cas que l’authentification multifacteur n’est pas réussie.  Vous pouvez également spécifier le délai d’expiration de session en minutes.
Activer la journalisation|Active la journalisation dans le portail d’utilisateur.  Les fichiers journaux se trouvent dans : C:\Program Files\Multi-Factor Authentication Server\Logs.

La plupart de ces paramètres sont visibles par l’utilisateur lorsqu’elles sont activées et les signes utilisateur dans le portail d’utilisateur.

![Paramètres du portail utilisateur](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Pour configurer les paramètres du portail utilisateur sur le serveur de l’authentification multifacteur Azure




1. Dans le serveur de l’authentification multifacteur Azure, cliquez sur l’icône du portail de l’utilisateur. Sous l’onglet Paramètres, entrez l’URL pour le portail d’utilisateur dans la zone de texte URL du portail utilisateur. Cette URL est insérée dans les messages électroniques qui sont envoyés aux utilisateurs lorsqu’elles sont importées dans le serveur de l’authentification multifacteur Azure si la fonctionnalité de messagerie a été activée.
2. Choisissez les paramètres que vous souhaitez utiliser dans le portail d’utilisateur. Par exemple, si les utilisateurs sont autorisés à contrôler les méthodes d’authentification, assurez-vous qu’Autoriser les utilisateurs à sélectionner la méthode est activée ainsi que les méthodes qu’ils sont proposées.
3. Cliquez sur le lien aide dans le coin supérieur droit pour comprendre les paramètres affichés.

<center>![Programme d’installation](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Onglet administrateurs
Cet onglet simplement vous permet d’ajouter des utilisateurs ayant des privilèges d’administrateur.  Lorsque vous ajoutez un administrateur, vous pouvez régler les autorisations qu’ils reçoivent.  Ainsi, vous pouvez veillez à accordez uniquement les autorisations nécessaires à l’administrateur.  Cliquez simplement sur le bouton Ajouter et sélectionnez et utilisateur et les autorisations, puis sur Ajouter.

![Administrateurs de portail utilisateur](./media/multi-factor-authentication-get-started-portal/admin.png)


## <a name="security-questions"></a>Questions sur la sécurité
Cet onglet vous permet de spécifier les questions de sécurité que les utilisateurs doivent fournir des réponses à si les questions de sécurité d’utilisation pour l’option de secours est sélectionnée.  Serveur d’authentification multifacteur Azure est fourni avec les questions par défaut que vous pouvez utiliser.  Vous pouvez également modifier l’ordre ou ajouter vos propres questions.  Lorsque vous ajoutez vos propres questions, vous pouvez spécifier la langue que vous souhaitez que ces question apparaissent également dans.

![Questions sur la sécurité du portail utilisateur](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## <a name="passed-sessions"></a>Sessions passées

## <a name="saml"></a>SAML
Permet de vous permettent de configurer le portail d’utilisateur pour accepter les affirmations d’un fournisseur d’identité SAML.  Vous pouvez spécifier la session de délai d’attente, spécifiez le certificat de vérification et le journal des URL de redirection.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>Adresses IP autorisées
Cet onglet vous permet de spécifier des adresses IP uniques ou des plages d’adresses IP qui peuvent être ajoutés afin que si un utilisateur est connecté à partir d’une de ces adresses IP, l’authentification multifacteur est ignorée.

![Adresses IP autorisées portail de l’utilisateur](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Inscription d’un utilisateur libre-service
Si vous voulez que vos utilisateurs à se connecter et s’inscrire, vous devez sélectionner Autoriser les utilisateurs à se connecter dans et autoriser les options d’inscription d’un utilisateur. N’oubliez pas que les paramètres que vous sélectionnez affecte l’expérience de connexion utilisateur.

Par exemple, lorsqu’un utilisateur se connecte au portail utilisateur clique sur le bouton se connecter, ils sont puis accéder à la page le programme d’installation utilisateur l’authentification multifacteur Azure.  Selon la façon dont vous avez configuré l’authentification multifacteur Azure, l’utilisateur peut être en mesure de sélectionner leur méthode d’authentification.  

S’ils sélectionner la méthode d’authentification appel vocal ou ont été préconfigurée de manière à utiliser cette méthode, la page invitera l’utilisateur à entrer son numéro de téléphone principal et extension le cas échéant.  Ils peuvent également être autorisés à entrer un numéro de téléphone de sauvegarde.  

![Adresses IP autorisées portail de l’utilisateur](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Si l’utilisateur est requis pour utiliser un code confidentiel lors de l’authentification, la page également les invite à entrer un code confidentiel.  Après avoir entré leurs ou les numéros de téléphone et le code confidentiel (le cas échéant), l’utilisateur clique sur l’appel Me maintenant au bouton authentifier.  L’authentification multifacteur Azure effectue une authentification appeler au numéro de téléphone principal de l’utilisateur.  L’utilisateur doit répondre à l’appel téléphonique et entrez son code confidentiel (le cas échéant) et appuyez sur n pour passer à l’étape suivante de la procédure d’inscription automatique.   

Si l’utilisateur sélectionne la méthode d’authentification texte SMS ou a été préconfigurée de manière à utiliser cette méthode, la page invitera l’utilisateur à son numéro de téléphone mobile.  Si l’utilisateur est requis pour utiliser un code confidentiel lors de l’authentification, la page également les invite à entrer un code confidentiel.  Après avoir entré leur numéro de téléphone et le code confidentiel (le cas échéant), l’utilisateur clique sur le texte Me maintenant au bouton authentifier.  L’authentification multifacteur Azure effectue une authentification SMS au téléphone portable de l’utilisateur.  L’utilisateur doit recevoir le service SMS qui contient celui-heure-code secret (unique) et répondre au message avec cette unique plus son code confidentiel, le cas échéant) pour passer à l’étape suivante de la procédure d’inscription automatique.

![Portail de l’utilisateur SMS](./media/multi-factor-authentication-get-started-portal/text.png)   

Si l’utilisateur sélectionne la méthode d’authentification de l’application Mobile ou a été préconfigurée de manière à utiliser cette méthode, la page vous invite à l’utilisateur pour installer l’application de l’authentification multifacteur Azure sur leur appareil et générer un code d’activation.  Après avoir installé l’application de l’authentification multifacteur Azure, l’utilisateur clique sur le bouton Générer le Code d’Activation.    

>[AZURE.NOTE]Pour pouvoir utiliser l’application de l’authentification multifacteur Azure, l’utilisateur doit activer les notifications push pour leur appareil.

La page affiche ensuite un code d’activation et une URL ainsi que d’une image de code-barres.  Si l’utilisateur est requis pour utiliser un code confidentiel lors de l’authentification, la page également les invite à entrer un code confidentiel.  L’utilisateur entre le code d’activation et l’URL à l’application de l’authentification multifacteur Azure ou utilise l’Analyseur de code-barres pour numériser l’image de code-barres et clique sur le bouton Activer.    

Une fois l’activation terminée, l’utilisateur clique sur le bouton authentifier maintenant.  L’authentification multifacteur Azure effectue une authentification pour l’application mobile de l’utilisateur.  L’utilisateur doit entrer son code confidentiel (le cas échéant) et appuyez sur le bouton authentifier dans son application mobile pour passer à l’étape suivante de la procédure d’inscription automatique.  


Si les administrateurs ont configuré le serveur de l’authentification multifacteur Azure pour recueillir les réponses et les questions de sécurité, l’utilisateur est considérée puis à la page de questions sur la sécurité.  L’utilisateur doit sélectionner quatre questions de sécurité et fournir des réponses à leurs questions sélectionnées.    

![Questions sur la sécurité du portail utilisateur](./media/multi-factor-authentication-get-started-portal/secq.png)  

L’inscription automatique utilisateur est terminée et l’utilisateur est connecté au portail utilisateur.  Les utilisateurs peuvent se reconnecter pour le portail d’utilisateur à tout moment à l’avenir pour modifier leurs numéros de téléphone, les codes confidentiels, les méthodes d’authentification et les questions sur la sécurité si autorisées par leurs administrateurs.
