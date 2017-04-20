<properties
    pageTitle="Serveur de l’authentification Multifacteur avec Windows Server 2012 R2 AD FS | Microsoft Azure"
    description="Cet article décrit comment commencer à utiliser l’authentification multifacteur Azure et AD FS dans Windows Server 2012 R2."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>


# <a name="secure-your-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-in-windows-server-2012-r2"></a>Protéger vos ressources de cloud et en local à l’aide de serveur de l’authentification multifacteur Azure avec AD FS dans Windows Server 2012 R2

Si vous utilisez les Services de fédération Active Directory (AD FS) et que vous souhaitez cloud sécurisé ou ressources locales, vous pouvez configurer le serveur de l’authentification multifacteur Azure pour fonctionner avec AD FS. Cette configuration permet de déclencher la vérification des points de terminaison de grande valeur.

Dans cet article, nous aborderons à l’aide de serveur de l’authentification multifacteur Azure avec AD FS dans Windows Server 2012 R2. Pour plus d’informations, découvrez comment faire pour [sécuriser les ressources cloud et en local à l’aide de serveur de l’authentification multifacteur Azure avec AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## <a name="secure-windows-server-2012-r2-ad-fs-with-azure-multi-factor-authentication-server"></a>Sécuriser Windows Server 2012 R2 AD FS avec l’authentification multifacteur Azure serveur

Lorsque vous installez serveur de l’authentification multifacteur Azure, vous disposez des options suivantes :

- Installer serveur de l’authentification multifacteur Azure localement sur le même serveur que AD FS
- Installez la carte de l’authentification multifacteur Azure localement sur le serveur AD FS, puis serveur d’authentification multifacteur sur un autre ordinateur

Avant de commencer, gardez les informations suivantes :

- Vous n’êtes pas obligé d’Installer serveur de l’authentification multifacteur Azure sur le serveur AD FS. Toutefois, vous devez installer la carte de l’authentification multifacteur pour AD FS sur un Windows Server 2012 R2 exécutant AD FS. Vous pouvez installer le serveur sur un autre ordinateur si elle est une version prise en charge et que vous installez la carte AD FS séparément sur votre serveur de fédération AD FS. Consultez les procédures suivantes pour découvrir comment installer la carte séparément.

- Lorsque la carte de l’authentification Multifacteur serveur AD FS a été conçue, il a été prévu que AD FS transmettre le nom de la partie de confiance vers la carte. Puis, le nom de fête confiance peut être utilisé comme un nom d’application. Toutefois, cela ne ne doivent ne pas la casse. Si votre organisation utilise message texte ou méthodes de vérification de l’application mobile, les chaînes définis dans les paramètres de la société contiennent un espace réservé, <*nom de l’application*$ $>. Cet espace réservé n’est pas remplacé automatiquement lorsque vous utilisez la carte AD FS. Nous vous conseillons de supprimer l’espace réservé à partir des chaînes appropriés lorsque vous sécurisez AD FS.

- Le compte que vous utilisez pour vous connecter doit disposer de droits d’utilisateur pour créer des groupes de sécurité dans votre service d’annuaire Active Directory.

- L’Assistant installation de l’authentification multifacteur AD FS carte crée un groupe de sécurité appelé PhoneFactor Admins dans votre instance d’Active Directory. Il ajoute ensuite le compte du service de votre service de fédération AD FS à ce groupe. Nous vous recommandons de vérifier sur votre contrôleur de domaine que le groupe PhoneFactor Admins est bien créé et que le compte de service les AD FS est un membre de ce groupe. Si nécessaire, ajoutez manuellement le compte de service AD FS au groupe PhoneFactor Admins sur votre contrôleur de domaine.

- Pour plus d’informations sur l’installation du Kit de développement de Service Web avec le portail d’utilisateur, en savoir plus sur [déploiement le portail d’utilisateur pour le serveur de l’authentification multifacteur Azure.](multi-factor-authentication-get-started-portal.md)


### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Installer serveur de l’authentification multifacteur Azure localement sur le serveur AD FS

1. Téléchargez et installez le serveur de l’authentification multifacteur Azure sur votre serveur AD FS. Pour plus d’informations d’installation, Découvrez [prise en main serveur de l’authentification multifacteur Azure](multi-factor-authentication-get-started-server.md).
2. Dans la console de gestion de serveur de l’authentification multifacteur Azure, cliquez sur l’icône **AD FS** , puis sélectionnez les options de **l’inscription utilisateur Autoriser** et **Autoriser les utilisateurs à sélectionner la méthode**.
3. Sélectionnez les options supplémentaires que vous souhaitez spécifier pour votre organisation.
4. Cliquez sur **installer AD FS carte**.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Si la fenêtre Active Directory est affichée, cela signifie que deux choses. Votre ordinateur est joint à un domaine, et la configuration d’Active Directory pour sécuriser les communications entre la carte AD FS et le service d’authentification multifacteur est incomplète. Cliquez sur **suivant** pour terminer cette configuration automatiquement, ou sélectionnez le **Ignorer la configuration automatique Active Directory et configurer les paramètres manuellement** case à cocher, puis cliquez sur **suivant**.
6. Si les fenêtres de groupe Local est affiché, cela signifie que deux choses. Votre ordinateur n’est pas joint à un domaine, et la configuration du groupe local pour sécuriser les communications entre la carte AD FS et le service d’authentification multifacteur est incomplète. Cliquez sur **suivant** pour terminer cette configuration automatiquement, ou sélectionnez le **Ignorer la configuration automatique groupe locale et configurer les paramètres manuellement** case à cocher, puis cliquez sur **suivant**.
7. Dans l’Assistant installation, cliquez sur **suivant**. Serveur de l’authentification multifacteur Azure crée le groupe PhoneFactor Admins et ajoute le compte de service AD FS au groupe PhoneFactor Admins.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Dans la page **Lancer le programme d’installation** , cliquez sur **suivant**.
9. Dans le programme d’installation de la carte de l’authentification multifacteur AD FS, cliquez sur **suivant**.
10. Une fois l’installation terminée, cliquez sur **Fermer** .
11. Lorsque la carte a été installée, vous devez l’enregistrer avec AD FS. Ouvrez Windows PowerShell et exécutez la commande suivante :<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Pour utiliser votre carte récemment enregistrée, modifier la stratégie d’authentification global dans AD FS. Dans la console de gestion AD FS, accédez au nœud de **Stratégies d’authentification** . Dans la section **Authentification multifacteur** , cliquez sur le lien **Modifier** en regard de la section **Paramètres globaux** . Dans la fenêtre **Modifier la stratégie d’authentification globale** , sélectionnez **L’authentification multifacteur** comme une méthode d’authentification supplémentaires, puis cliquez sur **OK**. La carte est enregistrée comme WindowsAzureMultiFactorAuthentication. Redémarrez le service AD FS pour l’enregistrement prenne effet.

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

À ce stade, serveur d’authentification multifacteur est configuré pour être un fournisseur d’authentification supplémentaires à utiliser avec AD FS.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Installer une instance autonome de la carte AD FS à l’aide du Kit de développement de Service Web
1. Installez le Kit de développement de Service Web sur le serveur qui exécute serveur d’authentification multifacteur.
2. Copiez les fichiers suivants à partir de la \Program Files\Multi-directory facteur d’authentification serveur pour le serveur sur lequel vous envisagez d’installer la carte AD FS :
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Registre MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.ps1 Unregister
  - MultiFactorAuthenticationAdfsAdapter.config
3. Exécutez le programme d’installation MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Dans le programme d’installation de la carte de l’authentification multifacteur AD FS, cliquez sur **suivant** pour démarrer l’installation.
5. Une fois l’installation terminée, cliquez sur **Fermer** .

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Modifier le fichier MultiFactorAuthenticationAdfsAdapter.config

Suivez ces étapes pour modifier le fichier MultiFactorAuthenticationAdfsAdapter.config :

1. Définir le nœud **UseWebServiceSdk** **true**.  
2. Définissez la valeur pour **WebServiceSdkUrl** à l’URL du Kit de développement logiciel multifacteur authentification Web Service. Par exemple : *https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx*, où certificatename est le nom de votre certificat.  
3. Modifier le script de Registre MultiFactorAuthenticationAdfsAdapter.ps1 en ajoutant *- ConfigurationFilePath &lt;chemin d’accès&gt; * à la fin de la `Register-AdfsAuthenticationProvider` commande, où * &lt;chemin d’accès&gt; * est le chemin d’accès complet au fichier MultiFactorAuthenticationAdfsAdapter.config.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Configurer le Kit de développement de Service Web avec un nom d’utilisateur et mot de passe

Il existe deux options de configuration du Kit de développement de Service Web. La première est avec un nom d’utilisateur et mot de passe, le deuxième avec un certificat client. Procédez comme suit pour la première option, ou passer directement pour la deuxième.  

1. Définissez la valeur pour **WebServiceSdkUsername** à un compte qui est membre du groupe de sécurité PhoneFactor Admins. Utiliser la &lt;domaine&gt;& #92 ; &lt;nom d’utilisateur&gt; format.  
2. Définissez la valeur pour **WebServiceSdkPassword** le mot de passe du compte approprié.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Configurer le Kit de développement de Service Web avec un certificat client

Si vous ne voulez pas utiliser un nom d’utilisateur et mot de passe, procédez comme suit pour configurer le Kit de développement de Service Web avec un certificat client.

1. Obtenir un certificat client à partir d’une autorité de certification pour le serveur qui exécute le Kit de développement de Service Web. Découvrez comment [obtenir des certificats clients](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importer le certificat client dans le magasin de certificats personnels ordinateur local sur le serveur qui exécute le Kit de développement de Service Web. Assurez-vous que certificat public de l’autorité de certification se trouve dans le magasin de certificats des certificats racines approuvés.  
3. Exportez les clés privées et du certificat client vers un fichier .pfx.  
4. Exporter la clé publique au format en base 64 vers un fichier .cer.  
5. Dans le Gestionnaire de serveur, vérifiez que la fonctionnalité de mappage d’authentification par certificat \Web Server\Security\IIS serveur Web (IIS) est installée. S’il n’est pas installé, sélectionnez **Ajouter des rôles et les fonctionnalités** d’ajouter cette fonctionnalité.  
6. Dans le gestionnaire, double-cliquez sur **L’éditeur de Configuration** dans le site Web qui contient le répertoire virtuel SDK du Service Web. Il est important effectuer cette action au niveau du site Web et non au niveau du répertoire virtuel.  
7. Accédez à la section **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** .  
8. Définissez enabled **true**.  
9. Définissez oneToOneCertificateMappingsEnabled sur **true**.  
10. Cliquez sur le bouton **...** en regard d’oneToOneMappings, puis cliquez sur le lien **Ajouter** .  
11. Ouvrez le fichier .cer en base 64 que vous avez exporté précédemment. Supprimer *situés à commencer certificat situés à*, *situés à mettre fin à certificat situés à*et tous les sauts de ligne. Copiez la chaîne qui en résulte.  
12. Certificat de jeu à la chaîne copiée à l’étape précédente.  
13. Définissez enabled **true**.  
14. Définir le nom d’utilisateur à un compte qui est membre du groupe de sécurité PhoneFactor Admins. Utiliser la &lt;domaine&gt;& #92 ; &lt;nom d’utilisateur&gt; format.  
15. Définir le mot de passe pour le mot de passe du compte approprié, puis fermez l’éditeur de Configuration.  
16. Cliquez sur le lien **Appliquer** .  
17. Dans le répertoire virtuel SDK du Service Web, double-cliquez sur **l’authentification**.  
18. Vérifiez que l’emprunt d’identité ASP.NET et l’authentification de base sont définies sur **activé**, et que tous les autres éléments sont définis sur **désactivé**.  
19. Dans le répertoire virtuel SDK du Service Web, double-cliquez sur **Paramètres SSL**.  
20. Définissez les certificats clients sur **Accepter**, puis cliquez sur **Appliquer**.  
21. Copiez le fichier .pfx que vous avez exporté précédemment sur le serveur qui exécute la carte AD FS.  
22. Importer le fichier .pfx dans le magasin de certificats personnels ordinateur local.  
23. Avec le bouton droit et sélectionnez **Gérer les clés privées**puis octroyer l’accès en lecture sur le compte que vous avez utilisé pour vous connecter au service AD FS.  
24. Ouvrez le certificat client et copiez l’empreinte numérique à partir de l’onglet **Détails** .  
25. Dans le fichier MultiFactorAuthenticationAdfsAdapter.config, définissez **WebServiceSdkCertificateThumbprint** avec la chaîne copiée à l’étape précédente.  


Enfin, pour enregistrer la carte, exécutez la \Program Files\Multi-script facteur d’authentification Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 dans PowerShell. La carte est enregistrée comme WindowsAzureMultiFactorAuthentication. Redémarrez le service AD FS pour l’enregistrement prenne effet.

## <a name="related-topics"></a>Rubriques connexes

Pour obtenir de l’aide, voir le [FAQ de l’authentification multifacteur Azure](multi-factor-authentication-faq.md)
