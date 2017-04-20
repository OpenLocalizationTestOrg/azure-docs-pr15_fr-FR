<properties 
    pageTitle="Prise en main du Service l’authentification Multifacteur serveur Mobile application Web"
    description="L’application de l’authentification multifacteur Azure propose une option d’authentification de hors supplémentaires.  Il permet au serveur de l’authentification Multifacteur d’utiliser des notifications push aux utilisateurs."
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
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="getting-started-the-mfa-server-mobile-app-web-service"></a>Prise en main du Service l’authentification Multifacteur serveur Mobile application Web

L’application de l’authentification multifacteur Azure propose une option d’authentification de hors supplémentaires. Au lieu de placer un appel téléphonique automatisé ou un SMS à l’utilisateur pendant la connexion, l’authentification multifacteur Azure envoie une notification à l’application de l’authentification multifacteur Azure sur un smartphone ou une tablette de l’utilisateur. L’utilisateur clique simplement sur « Authentifier » (ou entre un code confidentiel et appui sur « Authentifier ») dans l’application pour se connecter.

Pour pouvoir utiliser l’application de l’authentification multifacteur Azure, les éléments suivants sont nécessaires pour que l’application peut communiquer avec le Service Web Mobile application :

- Voir configuration matérielle et logicielle pour matérielle et logicielle requise
- Vous devez utiliser 6.0 ou version ultérieure du serveur de l’authentification multifacteur Azure
- Service Web de l’application mobile doit être installé sur un serveur web via Internet exécutant Microsoft® Internet Information Services (IIS) IIS 7.x ou version ultérieure.  Pour plus d’informations sur IIS voir [IIS.NET](http://www.iis.net/).
- Vérifiez v4.0.30319 ASP.NET est installé, enregistré et affectez la valeur autorisé
- Services de rôle requis incluant ASP.NET et compatibilité avec la métabase IIS 6
- Le Service Web mobile application doit être accessible via une URL publique
- Service Web de l’application mobile doit être sécurisé avec un certificat SSL.
- Azure multifacteur authentification Web Service SDK doit être installé dans IIS 7.x ou version ultérieure sur le serveur que le serveur de l’authentification multifacteur Azure
- Azure multifacteur authentification Web Service SDK doivent être sécurisé avec un certificat SSL.
- Service Web de l’application mobile doit pouvoir se connecter au SDK Azure multifacteur authentification Web Service via le protocole SSL
- Service Web de l’application mobile doit pouvoir s’authentifier à l’Azure multifacteur authentification SDK du Service Web utilisant les informations d’identification d’un compte de service qui fait partie d’un groupe de sécurité nommé « PhoneFactor administrateurs ». Ce compte de service et le groupe existent dans Active Directory si le serveur de l’authentification multifacteur Azure s’exécute sur un serveur à un domaine. Ce compte de service et le groupe existent localement sur le serveur de l’authentification multifacteur Azure s’il n’est pas joint à un domaine.


Installez le portail d’utilisateur sur un serveur autre que le serveur de l’authentification multifacteur Azure requiert les trois étapes suivantes :

1. Installez le Kit de développement de service web
2. Installer le service web application mobile
3. Configurer les paramètres de l’application mobile sur le serveur de l’authentification multifacteur Azure
4. Activer l’application de l’authentification multifacteur Azure pour les utilisateurs finaux

## <a name="install-the-web-service-sdk"></a>Installez le Kit de développement de service web

Si le Service Web Azure multifacteur authentification SDK n’est pas déjà installé sur le serveur de l’authentification multifacteur Azure, accédez à ce serveur et ouvrez le serveur de l’authentification multifacteur Azure. Cliquez sur l’icône SDK du Service Web, cliquez sur le SDK du Service Web installer... bouton et suivez les instructions affichées. Le Kit de développement de Service Web doit être sécurisé avec un certificat SSL. Un certificat auto-signé est OK à cet effet, mais il doit être importés dans le magasin « Approuvés autorités de Certification racine » du compte d’ordinateur Local sur le serveur web portail utilisateur afin qu’il approuve ce certificat lors de l’initialisation de la connexion SSL.

<center>![Programme d’installation](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## <a name="install-the-mobile-app-web-service"></a>Installer le service web application mobile
Avant d’installer le service web application mobile, gardez les éléments suivants :

- Si le portail d’utilisateur d’authentification multifacteur Azure est déjà installé sur le serveur via Internet, le nom d’utilisateur, mot de passe et l’URL pour le Kit de développement de Service Web peuvent être copiées à partir du fichier web.config du portail de l’utilisateur.
- Il est utile ouvrir un navigateur web sur le serveur web via Internet et accédez à l’URL du Kit de développement de Service Web qui ont été entrées dans le fichier web.config. Si le navigateur peut accéder au service web avec succès, elle doit invite à entrer les informations d’identification. Entrez le nom d’utilisateur et mot de passe qui ont été entrées dans le fichier web.config exactement tel qu’il apparaît dans le fichier. Vérifiez qu’aucun certificat avertissements ou des erreurs ne sont affichés.
- Si un proxy inverse ou un pare-feu se trouve devant le serveur web Service Web de l’application Mobile et effectuez décharger SSL, vous pouvez modifier le fichier web.config de Service Web de l’application Mobile et ajouter la clé suivante pour le <appSettings> section afin que le Service Mobile application Web utilisent http au lieu de https. Toutefois SSL est encore nécessaire à partir de l’application Mobile pour le pare-feu/proxy inverse. <add key="SSL_REQUIRED" value="false"/>

### <a name="to-install-the-mobile-app-web-service"></a>Pour installer le service web application mobile

<ol>
<li>Ouvrez l’Explorateur Windows sur le serveur de l’authentification multifacteur Azure et accédez au dossier où le serveur de l’authentification multifacteur Azure est installé (par exemple, C:\Program Files\Azure l’authentification multifacteur). Choisir la version 32 bits ou 64 bits du fichier d’installation Azure multifacteur AuthenticationPhoneAppWebServiceSetup selon le cas pour le serveur qui seront installé sur le Service Web Mobile application. Copiez le fichier d’installation sur le serveur via Internet.</li>

<li>Sur le serveur web via Internet, le fichier d’installation doit être exécuté avec des droits d’administrateur. Le plus simple consiste à ouvrir une invite de commandes en tant qu’administrateur et accédez à l’emplacement où le fichier d’installation a été copié.</li>  

<li>Exécutez le programme d’installation AuthenticationMobileAppWebServiceSetup multifacteur, modifier le Site si vous le souhaitez et modifiez le répertoire virtuel à un nom court comme « PA ». Un nom court répertoire virtuel est recommandé dans la mesure où les utilisateurs doivent entrer l’URL du Service Web Mobile application dans l’appareil mobile lors de l’activation.</li>

<li>Après avoir terminé l’installation de la AuthenticationMobileAppWebServiceSetup multifacteur Azure, accédez à C:\inetpub\wwwroot\PA (ou répertoire approprié basé sur le nom du répertoire virtuel), puis modifiez le fichier web.config.</li>  

<li>Recherchez les clés WEB_SERVICE_SDK_AUTHENTICATION_USERNAME et WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD et définissez les valeurs pour le nom d’utilisateur et mot de passe du compte de service qui est un membre de la sécurité PhoneFactor Admins groupe (voir la section ci-dessus). Cela peut être le même compte utilisé en tant que l’identité du portail d’utilisateur de l’authentification multifacteur Azure si qui a été installé précédemment. Veillez à entrer le nom d’utilisateur et mot de passe entre les guillemets à la fin de la ligne (valeur = » « / >). Il est recommandé d’utiliser un nom d’utilisateur complet (par exemple, DOMAINE\nom d’utilisateur ou ordinateur om d’utilisateur).</li>  

<li>Recherchez le paramètre de l’application Web Service_pfwssdk_PfWsSdk pfMobile et définissez la valeur de « http://localhost:4898/PfWsSdk.asmx » à l’URL du Kit de développement de Service Web qui s’exécute sur le serveur de l’authentification multifacteur Azure (par exemple, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Étant donné que SSL est utilisé pour cette connexion, vous devez faire référence le Kit de développement de Service Web par le nom du serveur et non adresse IP étant donné que le certificat SSL ne sera ont été émis pour le nom du serveur et l’URL utilisée doit correspondre au nom sur le certificat. Si le nom du serveur ne résout pas à une adresse IP du serveur via Internet, ajoutez une entrée au fichier hosts sur ce serveur pour établir une correspondance entre le nom du serveur de l’authentification multifacteur Azure son adresse IP. Enregistrez le fichier web.config après que modifications ont été apportées.</li>  

<li>Si le site Web que le Service Web Mobile application a été installé sous (par exemple, Site Web par défaut) n’a pas encore été relié avec un certificat signé publiquement, installez le certificat sur le serveur si ce n’est pas déjà installé, ouvrez le Gestionnaire IIS et lier le certificat au site Web.</li>  

<li>Ouvrez un navigateur web à partir de n’importe quel ordinateur et accédez à l’URL où le Service Web Mobile application a été installé (par exemple, https://www.publicwebsite.com/PA). Vérifiez qu’aucun certificat avertissements ou des erreurs ne sont affichés.</li>

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurer les paramètres de l’application mobile sur le serveur de l’authentification multifacteur Azure
Maintenant que le service web application mobile est installé, vous devez configurer le serveur de l’authentification multifacteur Azure pour l’utiliser avec le portail.

#### <a name="to-configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Pour configurer les paramètres de l’application mobile sur le serveur de l’authentification multifacteur Azure

1. Dans le serveur de l’authentification multifacteur Azure, cliquez sur l’icône du portail de l’utilisateur. Si les utilisateurs sont autorisés à contrôler les méthodes d’authentification, sous l’onglet Paramètres, sous Autoriser les utilisateurs à sélectionner méthode, vérifiez l’application Mobile. Sans cette fonctionnalité est activée, les utilisateurs finaux doivent contacter votre service d’assistance pour terminer l’activation de l’application Mobile.
2. Vérifier l’autoriser les utilisateurs pour activer la zone de l’application Mobile.
3. Cochez la case Autoriser inscription d’un utilisateur.
4. Cliquez sur l’icône de l’application Mobile.
5. Entrez l’URL utilisée avec le répertoire virtuel qui a été créé lors de l’installation du AuthenticationMobileAppWebServiceSetup multifacteur Azure. Un nom de compte peut être entré dans l’espace réservé. Ce nom de société s’affichent dans l’application mobile. Si laissé vide, le nom de votre fournisseur d’authentification multifacteur créé dans le portail de gestion Azure s’affichera.



<center>![Programme d’installation](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>
