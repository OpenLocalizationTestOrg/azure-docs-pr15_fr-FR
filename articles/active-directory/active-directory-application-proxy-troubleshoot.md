<properties
    pageTitle="Résoudre les problèmes de Proxy d’Application | Microsoft Azure"
    description="Décrit comment résoudre les erreurs dans le Proxy d’Application Azure AD."
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
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="kgremban"/>



# <a name="troubleshoot-application-proxy"></a>Résoudre les problèmes de Proxy de l’Application

Si des erreurs se produisent dans l’accès à une application publiée ou publier des applications, consultez les options suivantes pour voir si Proxy de l’Application Microsoft Azure AD fonctionne correctement :

- Ouvrez la console Windows Services et vérifiez que le service **Microsoft AAD Application Proxy Connector** est activé et en cours d’exécution. Vous pouvez également consulter la page de propriétés de service Proxy de l’Application, comme le montre l’image suivante :  
  ![Capture d’écran de la fenêtre Propriétés du connecteur Microsoft AAD Application Proxy](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)

- Ouvrez l’Observateur d’événements et recherchez des événements relatifs aux connecteurs Proxy de l’Application dans les **Applications et Services journaux** > **Microsoft** > **AadApplicationProxy** > **connecteur** > **administrateur**.
- Si nécessaire, journaux plus détaillées sont disponibles en activant analytique et débogage journaux et en activant le journal de sessions connecteur Proxy de l’Application.

## <a name="the-page-is-not-rendered-correctly"></a>La page ne s’affiche pas correctement

Si vous ne recevez un message d’erreur spécifique, vous devrez toujours des problèmes avec votre application rendu ou ne fonctionne pas correctement. Cela peut se produire si vous avez publié le chemin d’accès de l’article, mais l’application requiert le contenu existant à l’extérieur de ce chemin d’accès.

Par exemple, si vous publiez le https://yourapp/app chemin d’accès, mais l’application appelle images dans https://yourapp/media, ils ne sont pas être rendus. Vérifiez que vous publiez l’application en utilisant le chemin d’accès au niveau le plus élevé que vous devez inclure le contenu approprié. Dans cet exemple, il serait http://yourapp/.

Si vous modifiez votre chemin d’accès pour inclure le contenu référencé, mais avez encore besoin d’utilisateurs sur un lien dans le chemin d’accès plus approfondie, consultez le blog [définissant le lien droit pour les applications de Proxy de l’Application dans le panneau d’accès Azure AD et le Lanceur d’applications Office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="general-errors"></a>Erreurs générales

Erreur | Description | Résolution
--- | --- | ---
Cette application d’entreprise ne sont pas accessibles. Vous n’êtes pas autorisé à accéder à cette application. Échoué de l’autorisation. Veillez à affecter l’utilisateur ayant accès à cette application. | Vous n’avez pas avoir désigné l’utilisateur pour cette application. | Accédez à l’onglet **d’Application** , puis sous **utilisateurs et groupes**, affectez cet utilisateur ou groupe d’utilisateurs à cette application.
Cette application d’entreprise ne sont pas accessibles. Vous n’êtes pas autorisé à accéder à cette application. Échoué de l’autorisation. Assurez-vous que l’utilisateur possède une licence pour Azure Active Directory Premium ou de base. | Cette erreur peuvent se produire vos utilisateurs lorsque vous tentez d’accéder à l’application que vous avez publié s’ils n’ont pas été explicitement affectés possédant une licence Premium/Basic par l’administrateur de l’abonné. | Accédez à Active Directory **licences** onglet l’abonné et vérifiez que cet utilisateur ou un groupe est affecté une prime ou une licence de base.


## <a name="connector-troubleshooting"></a>Résolution des problèmes de connecteur
Si l’enregistrement échoue lors de l’installation de l’Assistant Connecteur, il existe deux manières d’afficher la raison de l’échec. Rechercher dans le journal des événements sous **des Applications et Services Logs\Microsoft\AadApplicationProxy\Connector\Admin**, soit exécuter la commande Windows PowerShell suivante.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| Erreur | Description | Résolution |
| --- | --- | --- |
| Échoué de l’enregistrement du connecteur : Vérifiez que vous avez activé le Proxy d’Application dans le portail de gestion Azure et que vous avez entré votre nom d’utilisateur Active Directory et le mot de passe correctement. Erreur : « une ou plusieurs erreurs se sont produites. » | Vous avez fermé la fenêtre enregistrement sans effectuer de connexion à Azure Active Directory. | Réexécutez l’Assistant Connecteur et enregistrer le lien. |
| Échoué de l’enregistrement du connecteur : Vérifiez que vous avez activé le Proxy d’Application dans le portail de gestion Azure et que vous avez entré votre nom d’utilisateur Active Directory et le mot de passe correctement. Erreur : « AADSTS50001 : ressources `https://proxy.cloudwebappproxy.net /registerapp` est désactivé. » | Proxy de l’application est désactivé.  | Veillez à qu'activer le Proxy d’Application dans le portail classique Azure avant d’essayer d’enregistrer le lien. Pour plus d’informations sur l’activation du Proxy de l’Application, voir [Activer le Proxy d’Application services](active-directory-application-proxy-enable.md). |
| Échoué de l’enregistrement du connecteur : Vérifiez que vous avez activé le Proxy d’Application dans le portail de gestion Azure et que vous avez entré votre nom d’utilisateur Active Directory et le mot de passe correctement. Erreur : « une ou plusieurs erreurs se sont produites. » | Si la fenêtre d’enregistrement s’ouvre, puis se ferme sans vous permettant de se connecter, vous obtiendrez probablement cette erreur. Cette erreur se produit lorsqu’il y a une erreur réseau sur votre système. | Assurez-vous qu’il est possible de se connecter à partir d’un navigateur sur un site Web public et que les ports sont ouverts comme indiqué dans les [conditions préalables pour les Proxy de l’Application](active-directory-application-proxy-enable.md). |
| Échoué de l’enregistrement du connecteur : Vérifiez que votre ordinateur est connecté à Internet. Erreur : « aucun point de terminaison à l’écoute sur est survenu `https://connector.msappproxy.net :9090/register/RegisterConnector` qui peut accepter le message. Cela est dû souvent une adresse incorrecte ou une action SOAP. Le cas échéant, pour plus d’informations, consultez InnerException,. » | Si vous me connecter à l’aide de votre nom d’utilisateur Azure AD et le mot de passe mais puis recevez ce message d’erreur, il peut être que tous les ports au-dessus 8081 sont bloqués. | Vérifiez que les ports nécessaires sont ouverts. Pour plus d’informations, voir [conditions préalables pour les Proxy de l’Application](active-directory-application-proxy-enable.md). |
| Erreur clairs est présentée dans la fenêtre d’enregistrement. Ne peut pas continuer – uniquement pour fermer la fenêtre. | Vous avez entré le mauvais nom d’utilisateur ou mot de passe. | Réessayez. |
| Échoué de l’enregistrement du connecteur : Vérifiez que vous avez activé le Proxy d’Application dans le portail de gestion Azure et que vous avez entré votre nom d’utilisateur Active Directory et le mot de passe correctement. Erreur : « AADSTS50059 : aucune information d’identification client figurant dans des options de la requête ou défini par les informations d’identification fournies et recherche en principe service URI a échoué. | Vous tentez d’ouvrir une session en utilisant un Account Microsoft et non à un domaine qui fait partie de l’ID d’organisation de l’annuaire que vous essayez d’accéder. | Vérifiez que l’administrateur fait partie du même nom de domaine comme domaine client, par exemple, si le domaine AD Azure est contoso.com, l’administrateur doit être admin@contoso.com. |
| Impossible de récupérer la stratégie d’exécution en cours d’exécution de scripts PowerShell. | Si l’installation de connecteur échoue, vérifiez que la stratégie d’exécution PowerShell n’est pas désactivé. | Ouvrez l’éditeur de stratégie de groupe. Accédez à la **Configuration de l’ordinateur** > **Modèles d’administration** > **Composants Windows** > **Windows PowerShell** et double-cliquez sur **Activer l’exécution de scripts**. Cela peut être défini à **Non configuré** ou **activé**. Si défini sur **activé**, assurez-vous que sous Options, la stratégie d’exécution est définie pour l' **Autoriser locales et les scripts à distance signé** ou pour **Autoriser tous les scripts**. |
| Connecteur Échec du téléchargement de la configuration. | Certificat client du connecteur, qui est utilisé pour l’authentification, expiré. Cela peut également se produire si vous avez le connecteur installé derrière un proxy. Dans ce cas, le connecteur ne peut pas accéder à Internet et ne seront pas en mesure de proposer des applications aux utilisateurs distants. | Renouveler la confidentialité manuellement à l’aide de la `Register-AppProxyConnector` applet de commande dans Windows PowerShell. Si le lien est derrière un proxy, il est nécessaire d’octroyer l’accès à Internet pour les comptes connecteur « services de réseau » et « système local ». Il est possible par leur accorder l’accès au serveur Proxy ou en définissant les pour ignorer le proxy. |
| Échoué de l’enregistrement du connecteur : Assurez-vous que vous êtes un administrateur Global de votre Active Directory pour enregistrer le lien. Erreur : « la demande d’enregistrement a été refusée. » | L’alias que vous tentez de vous connecter à l’aide n’est pas un administrateur sur ce domaine. Le lien est toujours installé pour le répertoire propriétaire du domaine de l’utilisateur. | Vérifiez que l’administrateur vous essayez connectez-vous en tant que dispose des autorisations globales pour le client Azure AD.|


## <a name="kerberos-errors"></a>Erreurs Kerberos

| Erreur | Description | Résolution |
| --- | --- | --- |
| Impossible de récupérer la stratégie d’exécution en cours d’exécution de scripts PowerShell. | Si l’installation de connecteur échoue, vérifiez que la stratégie d’exécution PowerShell n’est pas désactivé. | Ouvrez l’éditeur de stratégie de groupe. Accédez à la **Configuration de l’ordinateur** > **Modèles d’administration** > **Composants Windows** > **Windows PowerShell** et double-cliquez sur **Activer l’exécution de scripts**. Cela peut être défini à **Non configuré** ou **activé**. Si défini sur **activé**, assurez-vous que sous Options, la stratégie d’exécution est définie pour l' **Autoriser locales et les scripts à distance signé** ou pour **Autoriser tous les scripts**. |
| 12008 - azure AD dépassé le nombre maximal de tentatives de l’authentification Kerberos autorisés au serveur principal. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et l’application du serveur principal, ou un problème de configuration de date et l’heure sur les deux ordinateurs. | Le serveur principal refusé les tickets Kerberos créé par Azure AD. Vérifiez que Azure AD et le serveur d’applications serveur principal sont configurées correctement. Assurez-vous que la configuration de la date et l’heure sur Azure Active Directory et le serveur principal d’application sont synchronisés. |
| 13016 - azure AD Impossible d’extraire un tickets Kerberos au nom de l’utilisateur, car il n’existe aucun UPN dans le jeton de bord ou dans les cookies access. | Il existe un problème de configuration STS. | Corriger la configuration des déclarations UPN dans le STS. |
| 13019 - azure AD Impossible d’extraire un tickets Kerberos au nom de l’utilisateur en raison de l’erreur API générale suivante. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et le serveur contrôleur de domaine ou un problème de configuration de date et l’heure sur les deux ordinateurs. | Le contrôleur de domaine refusé les tickets Kerberos créé par Azure AD. Vérifiez que Azure AD et le serveur principal d’application sont correctement configurés, notamment la configuration du nom principal de service. Vérifiez que l’annonce Azure est joint au même domaine comme contrôleur de domaine pour vous assurer que le contrôleur de domaine établit d’approbation avec Azure AD au domaine. Assurez-vous que la configuration de la date et l’heure sur la publicité Azure et le contrôleur de domaine sont synchronisés. |
| 13020 - azure AD Impossible d’extraire un tickets Kerberos au nom de l’utilisateur, car le nom principal de service du serveur principal n’est pas défini. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et le serveur contrôleur de domaine ou un problème de configuration de date et l’heure sur les deux ordinateurs. | Le contrôleur de domaine refusé les tickets Kerberos créé par Azure AD. Vérifiez que Azure AD et le serveur principal d’application sont correctement configurés, notamment la configuration du nom principal de service. Vérifiez que l’annonce Azure est joint au même domaine comme contrôleur de domaine pour vous assurer que le contrôleur de domaine établit d’approbation avec Azure AD au domaine. Assurez-vous que la configuration de la date et l’heure sur la publicité Azure et le contrôleur de domaine sont synchronisés. |
| 13022 - azure AD ne peut pas s’authentifier à l’utilisateur, car le serveur principal répond aux tentatives d’authentification Kerberos avec une erreur HTTP 401. | Cet événement peut indiquer une configuration incorrecte entre Azure AD et l’application du serveur principal, ou un problème de configuration de date et l’heure sur les deux ordinateurs. | Le serveur principal refusé les tickets Kerberos créé par Azure AD. Vérifiez que Azure AD et le serveur d’applications serveur principal sont configurées correctement. Assurez-vous que la configuration de la date et l’heure sur Azure Active Directory et le serveur principal d’application sont synchronisés. |
| Le site Web ne peut pas afficher la page. | Cette erreur peut se produire votre utilisateur lorsque vous tentez d’accéder à l’application que vous avez publié si l’application est une application IWA. Le nom défini principal de service de cette application peut être incorrect. | Pour les applications IWA : Assurez-vous que le nom principal de service configurée pour cette application est correct. |
| Le site Web ne peut pas afficher la page. | Cette erreur peut se produire votre utilisateur lorsque vous tentez d’accéder à l’application que vous avez publié si l’application est une application OWA. Cela peut être dû à une des opérations suivantes : <br> -Le nom défini principal de service de cette application est incorrect. <br> -L’utilisateur qui a tenté d’accéder à l’application utilise un compte Microsoft, plutôt que le compte d’entreprise approprié pour vous connecter, ou l’utilisateur est un utilisateur invité. <br> -L’utilisateur qui a tenté d’accéder à l’application n’est pas correctement définie pour cette application sur le côté local. | Les étapes pour atténuer en conséquence : <br> -Assurez-vous que le nom principal de service configurée pour cette application est correct. <br> -Assurez-vous que l’utilisateur se connecte à l’aide de leur propre compte d’entreprise qui correspond au domaine de l’application publiée. Invités et des utilisateurs Microsoft Account ne peuvent pas accéder aux applications de IWA. <br> -Assurez-vous que que cet utilisateur dispose des autorisations appropriées, telle que définie pour cette application et sur l’ordinateur local. |
| Cette application d’entreprise ne sont pas accessibles. Vous n’êtes pas autorisé à accéder à cette application. Échoué de l’autorisation. Veillez à affecter l’utilisateur ayant accès à cette application. | Cette erreur peuvent se produire vos utilisateurs lorsque vous tentez d’accéder à l’application que vous avez publié s’ils utilisent des comptes Microsoft au lieu de leur propre compte d’entreprise pour vous connecter. Les utilisateurs invités peuvent également ouvrir cette erreur. | Invités et Account Microsoft les utilisateurs ne peuvent pas accéder aux applications de IWA. Vérifiez que l’utilisateur se connecte à l’aide de leur propre compte d’entreprise qui correspond au domaine de l’application publiée. |
| Cette application d’entreprise ne sont pas accessibles immédiatement. Réessayez plus tard... Le connecteur a expiré. | Cette erreur peuvent se produire vos utilisateurs lorsque vous tentez d’accéder à l’application que vous avez publié s’ils ne sont pas définis correctement pour cette application sur le côté local. | Assurez-vous que vos utilisateurs disposent des autorisations appropriées, telle que définie pour cette application et sur l’ordinateur local. |


## <a name="see-also"></a>Voir aussi

- [Activer le Proxy d’Application pour Azure Active Directory](active-directory-application-proxy-enable.md)
- [Publier des applications avec le Proxy d’Application](active-directory-application-proxy-publish.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)

Pour les dernières nouvelles et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
