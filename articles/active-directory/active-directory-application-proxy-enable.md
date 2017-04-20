<properties
    pageTitle="Activer le Proxy d’Application Azure AD | Microsoft Azure"
    description="Activer le Proxy d’Application dans le portail classique Azure et installer les connecteurs pour le serveur proxy inverse."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>

# <a name="enable-application-proxy-in-the-azure-portal"></a>Activer le Proxy d’Application dans le portail Azure

Cet article vous explique comment procéder pour activer le Proxy de l’Application Microsoft Azure AD pour votre annuaire cloud dans Azure Active Directory.

Si vous ne maîtrisez pas le Proxy d’Application peut vous aider à suivre, en savoir plus sur [comment fournir un accès à distance sécurisé aux applications sur site](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Conditions préalables application Proxy
Avant de pouvoir activer et utiliser les services Proxy de l’Application, vous devez disposer :

- Un [abonnement à Microsoft Azure AD base ou premium](active-directory-editions.md) et un répertoire Azure AD pour lesquels vous êtes un administrateur global.
- Un serveur exécutant Windows Server 2012 R2 ou Windows 8.1 ou versions ultérieures, sur lequel vous pouvez installer l’Application Proxy Connector. Le serveur envoie des requêtes aux services Proxy de l’Application dans le cloud, et il a besoin d’une connexion HTTP ou HTTPS vers les applications que vous publiez.

    - Pour de l’authentification unique à vos applications publiées, cet ordinateur doit être à un domaine dans le même domaine AD que les applications que vous publiez.

- Si le chemin d’accès est un pare-feu, vérifiez qu’elle est ouverte pour que le connecteur peut envoyer des demandes HTTPS (TCP) vers le Proxy d’Application. Le connecteur utilise ces ports avec sous-domaines qui font partie des domaines de niveau supérieur msappproxy.net et servicebus.windows.net. Assurez-vous d’ouvrir les ports suivants pour le trafic **sortant** :

  	| Numéro de port | Description |
  	| --- | --- |
  	| 80 | Activer le trafic HTTP sortant de validation de sécurité. |
  	| 443 | Activer l’authentification des utilisateurs par rapport à Azure AD (requis uniquement pour le processus d’inscription connecteur) |
  	| 10100 – 10120 | Activer les réponses HTTP métiers envoyées sur le serveur proxy |
  	| 9352, 5671 | Activer la communication entre le lien vers le service Azure des demandes entrantes. |
  	| 9350 | Facultatif, pour améliorer les performances des demandes entrantes |
  	| 8080 | Activer la séquence de démarrage de connecteur et la mise à jour automatique de connecteur |
  	| 9090 | Activer l’enregistrement de connecteur (requis uniquement pour le processus d’inscription connecteur) |
  	| 9091 | Activer le renouvellement automatique du certificat de gestion de la confidentialité connecteur |

    Si votre pare-feu applique le trafic conformément aux utilisateurs d’origine, ouvrez les ports pour le trafic en provenance de services de Windows qui s’exécutent comme un Service de réseau. En outre, vérifiez qu’activer le port 8080 pour NT\SYSTÈME.

- Si votre organisation utilise des serveurs proxy pour vous connecter à internet, veuillez consultez le billet de blog [travailler avec les serveurs proxy locaux existants](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/) pour plus d’informations sur la façon de les configurer.

## <a name="step-1-enable-application-proxy-in-azure-ad"></a>Étape 1 : Activer le Proxy d’Application dans Azure Active Directory
1. Se connecter en tant qu’administrateur dans le [portail classique Azure](https://manage.windowsazure.com/).
2. Accédez à Active Directory et sélectionnez le répertoire dans lequel vous souhaitez activer le Proxy d’Application.

    ![Active Directory - icône](./media/active-directory-application-proxy-enable/ad_icon.png)

3. Sélectionnez **configurer** dans la page annuaire et faites défiler jusqu'à **Proxy de l’Application**.
4. Activer/désactiver **Activer les Services Proxy d’Application pour ce répertoire** **activé**.

    ![Activer le Proxy d’Application](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. Sélectionnez **Télécharger maintenant**. Cela vous permet d’accéder à la **Azure AD Application Proxy connecteur télécharger**. Lisez et acceptez le contrat de licence et cliquez sur **Télécharger** pour enregistrer le fichier Windows Installer (.exe) pour le connecteur.

## <a name="step-2-install-and-register-the-connector"></a>Étape 2 : Installer et inscrire le connecteur
1. Exécutez **AADApplicationProxyConnectorInstaller.exe** sur le serveur que vous avez préparé selon les conditions préalables.
2. Suivez les instructions de l’Assistant d’installation.
3. Pendant l’installation, vous serez invité à enregistrer le lien avec le Proxy d’Application de votre client Azure AD.

  - Fournir vos informations d’identification de Azure AD administrateur général. Votre client de l’administrateur global peut être différent à partir de vos informations d’identification Microsoft Azure.
  - Vérifiez que l’administrateur qui enregistre le connecteur se trouve dans le même répertoire que vous avez activé le service Proxy de l’Application. Par exemple, si le domaine client est contoso.com, l’administrateur doit être admin@contoso.com ou tout autre alias sur ce domaine.
  - Si **La Configuration de sécurité renforcée d’Internet Explorer** est définie sur **On** sur le serveur où vous installez le connecteur, l’écran d’inscription peut-être être bloqué. Suivez les instructions dans le message d’erreur pour autoriser l’accès. Assurez-vous que la sécurité renforcée d’Internet Explorer est désactivé.
  - Si l’inscription de connecteur n’aboutit pas, voir [Résoudre les problèmes le Proxy d’Application](active-directory-application-proxy-troubleshoot.md).  

4. Une fois l’installation terminée, deux nouveaux services sont ajoutés à votre serveur :

    - **Microsoft AAD Application Proxy Connector** permet la connectivité
    - **Mise à jour Microsoft AAD Application Proxy connecteur** est un service de mise à jour automatique régulièrement vérifie de nouvelles versions du connecteur et met à jour le connecteur selon vos besoins.

    ![Services de Proxy Connector application - capture d’écran](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Cliquez sur **Terminer** dans la fenêtre d’installation.

À des fins de disponibilité, vous devez déployer au moins deux connecteurs. Pour déployer des connecteurs supplémentaires, répétez les étapes 2 et 3, ci-dessus. Chaque connecteur doit être enregistré séparément.

Si vous voulez désinstaller le connecteur, désinstallez le service du connecteur et le service de mise à jour. Redémarrez votre ordinateur pour supprimer complètement le service.


## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à [publier des applications avec Proxy de l’Application](active-directory-application-proxy-publish.md).

Si vous disposez d’applications qui se trouvent sur des réseaux distincts ou emplacements différents, vous pouvez utiliser des groupes de connecteur pour organiser les différents connecteurs en unités logiques. En savoir plus sur [l’utilisation des connecteurs Proxy de l’Application](active-directory-application-proxy-connectors.md).
