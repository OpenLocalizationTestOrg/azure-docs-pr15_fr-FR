<properties 
    pageTitle="Prise en main avec le serveur de l’authentification multifacteur Azure"
    description="Il s’agit de la page de l’authentification multifacteur Azure qui explique comment commencer à utiliser le serveur de l’authentification Multifacteur Azure."
    services="multi-factor-authentication"
    keywords="authentification serveur, azure à plusieurs facteur d’authentification application page activation, téléchargement de serveur d’authentification"
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

# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Prise en main avec le serveur de l’authentification multifacteur Azure




<center>![Cloud](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Maintenant que nous avons déterminé si à utiliser en local l’authentification multifacteur, passons accéder. Cette page couvre une nouvelle installation du serveur et l’obtention de configuration avec Active Directory local. Si vous avez déjà PhoneFactor server est installé et cherchez à mettre à niveau, voir [mise à niveau vers le serveur multifacteur Azure](multi-factor-authentication-get-started-server-upgrade.md) ou si vous recherchez des informations sur l’installation simplement le service web consultez [déploiement du Service Azure multifacteur d’authentification serveur Mobile application Web](multi-factor-authentication-get-started-server-webservice.md).


## <a name="download-the-azure-multi-factor-authentication-server"></a>Télécharger le serveur de l’authentification multifacteur Azure



Il existe deux façons différentes que vous pouvez télécharger le serveur de l’authentification multifacteur Azure. Les deux sont effectuées via le portail Azure. La première est en gérant le fournisseur d’authentification multifacteur directement. Le second est via les paramètres du service. La deuxième option nécessite un fournisseur d’authentification multifacteur ou une licence de l’authentification Multifacteur Azure, Azure AD Premium ou Enterprise mobilité Suite.


### <a name="to-download-the-azure-multi-factor-authentication-server-from-the-azure-portal"></a>Pour télécharger le serveur de l’authentification multifacteur Azure à partir du portail Azure
--------------------------------------------------------------------------------

1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sur la gauche, sélectionnez Active Directory.
3. Dans la page Active Directory, dans la partie supérieure, cliquez sur **Fournisseurs d’authentification multifacteur**
4. Cliquez sur **Gérer** en bas
5. Une nouvelle page s’ouvre.  Cliquez sur **télécharge.** 
 ![Télécharger](./media/multi-factor-authentication-sdk/download.png)
6. Au-dessus de **Générer les informations d’identification de l’Activation**, cliquez sur **télécharger.** 
 ![Télécharger](./media/multi-factor-authentication-get-started-server/download4.png)
7. Enregistrer le fichier téléchargé.



### <a name="to-download-the-azure-multi-factor-authentication-server-via-the-service-settings"></a>Pour télécharger le serveur de l’authentification multifacteur Azure via les paramètres du service


1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sur la gauche, sélectionnez Active Directory.
3. Double-cliquez sur votre instance d’Azure AD.
4. Dans la partie supérieure, cliquez sur **configurer**
![télécharger](./media/multi-factor-authentication-sdk/download2.png)
5. Sous l’authentification multifacteur, sélectionnez **Gérer les paramètres de service**
6. Dans la page Paramètres de services, dans la partie inférieure de l’écran, cliquez sur **accéder au portail**.
![Télécharger](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Une nouvelle page s’ouvre. Cliquez sur **télécharge.**
8. Au-dessus de **Générer les informations d’identification de l’Activation**, cliquez sur **télécharger.**
9. Enregistrer le fichier téléchargé.




## <a name="install-and-configure-the-azure-multi-factor-authentication-server"></a>Installer et configurer le serveur de l’authentification multifacteur Azure
À présent que vous avez téléchargé le serveur, vous pouvez installer et configurer.  N’oubliez pas que le serveur sur que vous l’installation remplit les conditions suivantes :



Configuration requise pour l’authentification multifacteur Azure Server|Description|
:------------- | :------------- |
Matériel|<li>200 Mo d’espace disque disponible</li><li>processeur capable x32 ou x64</li><li>1 Go de RAM ou plus</li>
Logiciel|<li>Windows Server 2008 ou version ultérieure si l’hôte est une système d’exploitation de serveur</li><li>Windows 7 ou version ultérieure si l’hôte est un système d’exploitation client</li><li>4.0 de Microsoft .NET Framework</li><li>Kit de développement IIS version 7.0 ou ultérieure si l’installation de l’utilisateur portail ou un service web</li>

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Configuration requise de pare-feu serveur d’authentification multifacteur Azure
--------------------------------------------------------------------------------
Chaque serveur de l’authentification Multifacteur doit être en mesure de communiquer sur le port 443 sortant à ce qui suit :

- https://PFD.phonefactor.NET
- https://pfd2.phonefactor.NET
- https://CSS.phonefactor.NET

Si le pare-feu sortants sont limitées sur le port 443, les plages d’adresses IP suivantes devez être ouvert :

RéseauIP|Masque de réseau|Plage d’adresses IP
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

Si vous n’utilisez pas de fonctionnalités Azure multifacteur authentification événement Confirmation et si les utilisateurs ne sont pas l’authentification avec les applications mobiles authentification multifacteur à partir d’appareils du réseau d’entreprise de l’adresse IP plages peuvent être réduits à ce qui suit :


RéseauIP|Masque de réseau|Plage d’adresses IP
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### <a name="to-install-and-configure-the-azure-multi-factor-authentication-server"></a>Pour installer et configurer le serveur de l’authentification multifacteur Azure
--------------------------------------------------------------------------------


1. Double-cliquez sur le fichier exécutable. Cela va commencer l’installation.
2. Dans l’écran Sélectionner un dossier d’Installation, assurez-vous que le dossier est correct, cliquez sur Suivant.
3. Une fois l’installation terminée, cliquez sur Terminer.  Cette action lance l’Assistant de configuration.
4. Dans l’Assistant configuration d’écran d’accueil, placez une vérification dans **sauter à l’aide de l’Assistant de Configuration de l’authentification** et cliquez sur **suivant**.  Vous fermez l’Assistant et démarrer le serveur.
    ![Cloud](./media/multi-factor-authentication-get-started-server/skip2.png)
5. Dans la page que nous téléchargé le serveur à partir de, cliquez sur le bouton **Générer les informations d’identification de l’Activation** .  Copiez ces informations dans le serveur de l’authentification Multifacteur Azure dans les zones appropriées et cliquez sur **Activer**.


Les étapes ci-dessus montrent une installation rapide avec l’Assistant configuration.  Vous pouvez réexécuter l’Assistant de l’authentification en le sélectionnant dans le menu Outils sur le serveur.



##<a name="import-users-from-active-directory"></a>Importer des utilisateurs depuis Active Directory

À présent que le serveur est installé et configuré, vous pouvez importer rapidement les utilisateurs sur le serveur de l’authentification Multifacteur Azure.

### <a name="to-import-users-from-active-directory"></a>Pour importer des utilisateurs depuis Active Directory
--------------------------------------------------------------------------------


1. Dans le serveur de l’authentification Multifacteur Azure, sur la gauche, sélectionnez **les utilisateurs**.
2. Dans la partie inférieure, sélectionnez **Importer à partir d’Active Directory**.
3. Maintenant vous pouvez rechercher des utilisateurs individuels ou effectuer une recherche dans l’annuaire Active Directory pour unités d’organisation avec des utilisateurs qu’ils.  Dans ce cas, nous spécifierez l’unité d’organisation des utilisateurs.
4. Sélectionnez tous les utilisateurs sur la droite, puis cliquez sur **Importer**.  Vous recevrez un message vous indiquant que vous avez réussi.  Fermez la fenêtre Importer.

![Cloud](./media/multi-factor-authentication-get-started-server/import2.png)

## <a name="send-users-an-email"></a>Envoyer aux utilisateurs un message électronique
Maintenant que vous avez importé vos utilisateurs au serveur de l’authentification multifacteur Azure, il est conseillé d’envoyer un message électronique les informant qu’ils ont été inscrite dans l’authentification multifacteur vos utilisateurs.

Avec le serveur de l’authentification multifacteur Azure, il existe différentes façons de configurer vos utilisateurs pour l’utilisation de l’authentification multifacteur.  Par exemple, si vous connaissez les numéros de téléphone des utilisateurs ont été en mesure d’importer les numéros de téléphone dans le serveur de l’authentification multifacteur Azure à partir du répertoire de leur entreprise, le message électronique permet aux utilisateurs de savoir qu’ils ont été configuré pour utiliser l’authentification multifacteur Azure, fournissent des instructions sur l’utilisation de l’authentification multifacteur Azure et informer l’utilisateur du numéro de téléphone sur que s’affichera leurs authentifications.  

Le contenu du message varient selon la méthode d’authentification qui a été définie pour l’utilisateur (par exemple, appel téléphonique, SMS, l’application mobile).  Par exemple, si l’utilisateur est requis pour utiliser un code confidentiel lors de l’authentification, le message électronique les indiquera que son code confidentiel initial a été défini.  Les utilisateurs doivent généralement modifier son code confidentiel pendant sa première authentification.

Si les numéros de téléphone des utilisateurs n’ont pas été configurés ou importées dans le serveur de l’authentification multifacteur Azure ou les utilisateurs sont préconfigurés de manière à utiliser l’application mobile pour l’authentification, vous pouvez leur envoyer un message électronique les informant qu’ils ont été configurés pour utiliser l’authentification multifacteur Azure et dirigera les pour terminer l’inscription de leur compte via le portail d’utilisateur d’authentification multifacteur Azure.  Un lien hypertexte sera inclus que l’utilisateur clique sur pour accéder au portail d’utilisateur. Lorsque l’utilisateur clique sur le lien hypertexte, leur navigateur web vous ouvrez et emportez-les à Azure multifacteur d’authentification utilisateur portail leur société.   


### <a name="configuring-email-and-email-templates"></a>Configurer la messagerie électronique et les modèles de courrier électronique

En cliquant sur l’icône messagerie électronique sur la gauche, vous pouvez configurer les paramètres d’envoi de ces messages électroniques.  Il s’agit de l’endroit où vous pouvez entrer des informations de votre serveur de messagerie SMTP et il vous permet d’envoyer un e-mail large ouverte en ajoutant une vérification de l’envoyer e-mails à case à cocher utilisateurs.

![Paramètres de messagerie](./media/multi-factor-authentication-get-started-server/email1.png)

Sous l’onglet messagerie contenu, vous verrez toutes les différents modèles de messagerie qui sont disponibles.  Selon la façon dont vous avez configuré vos utilisateurs pour utiliser l’authentification multifacteur, vous pouvez sélectionner le modèle qui conviennent le mieux vous convient le mieux.

![Modèles de courrier électronique](./media/multi-factor-authentication-get-started-server/email2.png)

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Traitement des données utilisateur par le serveur de l’authentification multifacteur Azure

Lorsque vous utilisez l’authentification multifacteur Multifacteur serveur local, données d’un utilisateur sont stockées dans les serveurs locale. Aucune donnée utilisateur permanente n’est stockée dans le cloud. Lorsque l’utilisateur effectue une authentification à deux facteurs, le serveur de l’authentification Multifacteur envoie des données au service cloud l’authentification Multifacteur Azure pour effectuer l’authentification. Lorsque ces demandes d’authentification sont envoyés au service cloud, les champs suivants sont envoyés dans la demande et journaux afin qu’ils soient disponibles dans les rapports d’authentification/utilisation du client. Certains champs sont facultatives afin qu’ils peuvent être activées ou désactivées au sein du serveur de l’authentification multifacteur. La communication à partir du serveur de l’authentification Multifacteur au service cloud l’authentification Multifacteur utilise SSL/TLS sur le port 443 sortant. Ces champs sont :

- Nº unique - nom d’utilisateur ou ID du serveur interne l’authentification Multifacteur
- Premier et dernier nom - facultatif
- Adresse de messagerie - facultatif
- Numéro de téléphone - lorsque vous effectuez un appel vocal ou authentification SMS
- Jeton appareil - lors d’une authentification de l’application mobile
- Mode d’authentification
- Résultat de l’authentification
- Nom de serveur de l’authentification Multifacteur
- Serveur de l’authentification Multifacteur IP
- Client IP – s’il est disponible



Outre les champs ci-dessus, le résultat de l’authentification (succès/refus) et le motif de refus de n’importe quel est stockée avec les données d’authentification et disponibles via les rapports d’authentification/utilisation.


## <a name="advanced-azure-multi-factor-authentication-server-configurations"></a>Les Configurations de serveur de l’authentification multifacteur Azure avancées
Pour plus d’informations sur la configuration avancée et informations de configuration, utilisez le tableau ci-dessous.

Méthode|Description
:------------- | :------------- |
[Portail de l’utilisateur](multi-factor-authentication-get-started-portal.md)|  Informations sur le programme d’installation et configuration du portail utilisateur, y compris le déploiement et accès libre aux utilisateurs.
[Service de fédération Active Directory](multi-factor-authentication-get-started-adfs.md)|Informations sur la configuration de l’authentification multifacteur Azure avec AD FS.
[Authentification RADIUS](multi-factor-authentication-get-started-server-radius.md)|  Informations sur le programme d’installation et configuration du serveur de l’authentification Multifacteur Azure avec RADIUS.
[Authentification IIS](multi-factor-authentication-get-started-server-iis.md)|Informations sur le programme d’installation et configuration du serveur de l’authentification Multifacteur Azure avec IIS.
[Authentification Windows](multi-factor-authentication-get-started-server-windows.md)|  Informations sur le programme d’installation et configuration du serveur de l’authentification Multifacteur Azure avec l’authentification Windows.
[Authentification LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informations sur le programme d’installation et configuration du serveur de l’authentification Multifacteur Azure avec l’authentification LDAP.
[À distance de la passerelle de bureau et de serveur de l’authentification multifacteur Azure à l’aide de RADIUS](multi-factor-authentication-get-started-server-rdg.md)|  Informations sur le programme d’installation et configuration du serveur de l’authentification Multifacteur Azure avec passerelle Bureau à distance à l’aide de RADIUS.
[Synchronisation avec Active Directory Windows Server](multi-factor-authentication-get-started-server-dirint.md)|Informations sur le programme d’installation et configuration de la synchronisation entre Active Directory et le serveur de l’authentification Multifacteur Azure.
[Déploiement du Service d’authentification multifacteur Azure serveur Web App Mobile](multi-factor-authentication-get-started-server-webservice.md)|Informations sur le programme d’installation et la configuration du service web de serveur de l’authentification Multifacteur Azure.
