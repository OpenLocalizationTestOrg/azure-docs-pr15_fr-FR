<properties
    pageTitle="Azure AD Connect : Résoudre les problèmes de connectivité | Microsoft Azure"
    description="Explique comment résoudre les problèmes de connectivité avec Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Résoudre les problèmes de connectivité avec Azure AD Connect
Cet article explique comment fonctionne la connectivité entre Azure AD Connect et Azure AD et comment résoudre les problèmes de connectivité. Ces problèmes sont susceptibles d’être vues dans un environnement avec un serveur proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Résoudre les problèmes de connectivité dans l’Assistant installation
Azure AD Connect utilise l’authentification moderne (à l’aide de la bibliothèque ADAL) pour l’authentification. L’Assistant installation et le moteur de synchronisation NOMPROPRE nécessitent machine.config doit être configuré correctement dans la mesure où il s’agit des applications .NET.

Dans cet article, nous vous montrent comment Fabrikam se connecte à Azure AD via son proxy. Le serveur proxy est nommé fabrikamproxy et utilise le port 8080.

Tout d’abord, nous avons besoin pour vous assurer que [**le fichier machine.config**](active-directory-aadconnect-prerequisites.md#connectivity) est configuré correctement.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

>[AZURE.NOTE]
Dans certains blogs non Microsoft il présentée modifications doivent être apportées au miiserver.exe.config à la place. Toutefois, ce fichier est remplacé sur chaque mise à niveau ainsi, même que si cela fonctionne lors de l’installation initiale, le système il s’interrompt sur la première mise à niveau. C’est pourquoi il est recommandé pour mettre à jour le fichier machine.config à la place.

Le serveur proxy doit comporter les URL requises ouvert. La liste officielle présentée dans [Office 365 URL et plages d’adresses IP ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

D'entre elles, le tableau suivant est au minimum absolu pour pouvoir vous connecter à Azure Active Directory du tout. Cette liste n’inclut pas les fonctionnalités facultatives, tels que votre mot de passe d’écriture différée ou la santé Azure AD se connecter. Il est décrit ici pour vous aider à résoudre les problèmes de configuration initiale.

URL | Port | Description
---- | ---- | ----
mscrl.Microsoft.com | HTTP/80 | Permet de télécharger des listes de révocation de certificats.
\*. verisign.com | HTTP/80 | Permet de télécharger des listes de révocation de certificats.
\*. entrust.com | HTTP/80 | Permet de télécharger des listes de révocation de certificats pour l’authentification Multifacteur.
\*. Windows.NET | HTTPS/443 | Permet de se connecter à Azure AD.
Secure.aadcdn.microsoftonline p.com | HTTPS/443 | Utilisé pour l’authentification Multifacteur.
\*. microsoftonline.com | HTTPS/443 | Utilisé pour configurer votre répertoire Azure AD et importer/exporter des données.

## <a name="errors-in-the-wizard"></a>Erreurs dans l’Assistant
L’Assistant installation utilise deux différents contextes de sécurité. Dans la page **se connecter à Azure AD** il est à l’aide de l’utilisateur actuellement connecté. Dans la page **configurer** il change pour [compte en cours d’exécution du service pour le moteur de synchronisation](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts). Les configurations de proxy garantie sont globales sur l’ordinateur s’il existe un problème, le problème est plus probablement apparaissent déjà dans la page **se connecter à Azure Active Directory** dans l’Assistant.

Il s’agit des erreurs les plus courantes que s’affiche dans l’Assistant installation.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>L’Assistant installation n’a pas été configuré correctement
Cette erreur s’affiche lorsque l’Assistant lui-même ne peut pas atteindre le serveur proxy.
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- Si vous voyez ce, vérifiez que le [fichier machine.config](active-directory-aadconnect-prerequisites.md#connectivity) n’a pas été correctement configuré.
- Si qui est à votre goût, suivez les étapes de [vérifier la connectivité proxy](#verify-proxy-connectivity) pour voir si le problème se produit à l’extérieur ainsi que l’Assistant.

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Le point de terminaison de l’authentification Multifacteur ne peuvent pas être atteintes
Cette erreur s’affichent si le point de terminaison **https://secure.aadcdn.microsoftonline-p.com** ne peuvent pas être atteintes et que votre administrateur général a l’authentification Multifacteur activé.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

- Si vous voyez ce, vérifiez que le point de terminaison secure.aadcdn.microsoftonline-p.com a été ajouté au serveur proxy.

### <a name="the-password-cannot-be-verified"></a>Le mot de passe ne peut pas être vérifiée
Si l’Assistant installation n’a réussi à se connecter à Azure AD, mais le mot de passe ne peut pas être vérifié, vous verrez ceci : ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- Est le mot de passe un mot de passe temporaire et doit être changé ? Il est réellement le mot de passe ? Essayez de se connecter à https://login.microsoftonline.com (sur un autre ordinateur que le serveur Azure AD Connect) et vérifiez que le compte est utilisable.

### <a name="verify-proxy-connectivity"></a>Vérifier la connectivité de proxy
Pour vérifier si le serveur Azure AD Connect possède une connexion réelle avec le serveur Proxy et Internet, nous utiliserons certaines PowerShell pour voir si le serveur proxy autorise les requêtes web ou non. Une invite PowerShell, exécutez `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Techniquement le premier appel consiste à https://login.microsoftonline.com et cela fonctionne également, mais l’autres URI est plus rapide de répondre.)

PowerShell utilisera la configuration dans le fichier machine.config contacter le serveur proxy. Les paramètres de winhttp/netsh doivent pas avoir un impact sur ces applets de commande.

Si le proxy est correctement configuré, vous devez obtenir un message de réussite : ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Si vous recevez **Impossible de se connecter au serveur distant** puis essayez de PowerShell effectuer un appel direct sans utiliser le serveur proxy ou DNS n’est pas configuré correctement. Vérifiez que le fichier **machine.config** est configuré correctement.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Si le serveur proxy n’est pas correctement configuré, nous allons une erreur : ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

Erreur | Texte d’erreur | Commentaire
---- | ---- | ---- |
403 | Interdit | Le proxy n’a pas été ouvert pour l’URL demandée. Reprenez la configuration du proxy et vérifiez que l' [URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) a été ouvert.
407 | Authentification du proxy obligatoire | Le serveur proxy requis se connecter et aucun a été fourni. Si votre serveur proxy requiert une authentification, assurez-vous d’avoir ces données configuré dans le fichier machine.config. Assurez-vous également que vous utilisez des comptes de domaine pour l’utilisateur qui exécute l’Assistant ainsi que pour le compte de service.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Le modèle de communication entre Azure AD Connect et Azure AD
Si vous avez suivi toutes ces étapes ci-dessus et fixes ne peut pas se connecter vous pourriez à ce stade commencer consultant journaux réseau. Cette section est de fournir un motif de connectivité normal et réussie. Il est également répertoriant harengs rouge courants qui peuvent être ignorés si vous lisez les journaux de réseau.

- Il sera appels vers https://dc.services.visualstudio.com. Il n’est pas nécessaire de cette ouvert dans le proxy pour l’installation pour réussir et celles-ci peuvent être ignorées.
- Vous verrez que la résolution dns permet de répertorier les hôtes réels pour être dans les autres espaces pas sous microsoftonline.com et la nsatc.net d’espace de nom DNS. Toutefois, il se trouvent pas les demandes de service web sur les noms des serveurs réel et vous n’êtes pas obligé d’ajoutez-les au serveur proxy.
- Les points de terminaison adminwebservice et provisioningapi (voir ci-dessous dans les journaux) sont les points de terminaison de découverte et utilisée pour déterminer le point de terminaison réel à utiliser et sera différent selon votre région.

### <a name="reference-proxy-logs"></a>Journaux proxy de référence
Voici une image à partir d’un journal proxy réel et la page de l’Assistant installation à partir de l’endroit où elle a été prise (entrées en double au même point de terminaison ont été supprimées). Cela peut servir comme référence pour votre propre journaux proxy et réseau. Les points de terminaison réels peuvent être différents dans votre environnement (notamment celles de *italique*).

**Se connecter à Active Directory Azure**

Heure | URL
--- | ---
1/11/2016 8:31 | Connect://login.microsoftonline.com:443
1/11/2016 8:31 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:32 | se connecter : / /*d’ancrage bba800*. microsoftonline.com:443
1/11/2016 8:32 | Connect://login.microsoftonline.com:443
1/11/2016 8:33 | Connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:33 | se connecter : / /*relais bwsc02*. microsoftonline.com:443

**Configurer**

Heure | URL
--- | ---
1/11/2016 8:43 | Connect://login.microsoftonline.com:443
1/11/2016 8:43 | se connecter : / /*bba800 ancre*. microsoftonline.com:443
1/11/2016 8:43 | Connect://login.microsoftonline.com:443
1/11/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | se connecter : / /*d’ancrage bba900*. microsoftonline.com:443
1/11/2016 8:44 | Connect://login.microsoftonline.com:443
1/11/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | se connecter : / /*d’ancrage bba800*. microsoftonline.com:443
1/11/2016 8:44 | Connect://login.microsoftonline.com:443
1/11/2016 8:46 | Connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:46 | se connecter : / /*relais bwsc02*. microsoftonline.com:443

**Synchronisation initiale**

Heure | URL
--- | ---
1/11/2016 8:48 | Connect://login.Windows.NET:443
1/11/2016 8:49 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:49 | se connecter : / /*bba900 ancre*. microsoftonline.com:443
1/11/2016 8:49 | se connecter : / /*d’ancrage bba800*. microsoftonline.com:443

## <a name="authentication-errors"></a>Erreurs d’authentification
Cette section décrit les erreurs qui peuvent être renvoyés à partir de la bibliothèque l’authentification utilisée par Azure AD Connect et PowerShell. L’erreur expliqué devrait vous être utile de comprendre les étapes suivantes.

### <a name="invalid-grant"></a>Accorder non valide
Nom d’utilisateur non valide ou mot de passe. Pour plus d’informations, voir [le mot de passe ne peuvent pas être vérifiée](#the-password-cannot-be-verified) .

### <a name="unknown-user-type"></a>Type d’utilisateur inconnu
Dans l’annuaire Active Directory Azure Impossible de trouver ou résolu. Peut-être vous tentez de vous connecter avec un nom d’utilisateur dans un domaine non vérifié ?

### <a name="user-realm-discovery-failed"></a>Découverte de domaine utilisateur a échoué
Problèmes de configuration de réseau ou un proxy. Le réseau ne peut pas être contacté, consultez [résolution des problèmes de connectivité dans l’Assistant installation](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Expiration du mot de passe utilisateur
Vos informations d’identification ont expiré. Modifier votre mot de passe.

### <a name="authorizationfailure"></a>AuthorizationFailure
Problème inconnu.

### <a name="authentication-cancelled"></a>Authentification annulée
Le défi l’authentification multifacteur (MFA) a été annulé.

### <a name="connecttomsonline"></a>ConnectToMSOnline
L’authentification a réussi, mais Azure AD PowerShell dispose d’un problème d’authentification.

### <a name="azurerolemissing"></a>AzureRoleMissing
L’authentification a réussi. Vous n’êtes pas un administrateur global.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
L’authentification a réussi. Gestion des identités dotés de privilèges a été activé et vous ne sont actuellement pas un administrateur global. Pour plus d’informations, voir [Gestion des identités dotés de privilèges](active-directory-privileged-identity-management-getting-started.md) .

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
L’authentification a réussi. Informations sur la société n’a pas pu être récupérer à partir d’Azure AD.

### <a name="retrievedomains"></a>RetrieveDomains
L’authentification a réussi. Informations sur le domaine n’a pas pu être récupérer à partir d’Azure AD.

## <a name="troubleshooting-steps-for-previous-releases"></a>Étapes de dépannage pour les versions précédentes.
L’assistant connexion a été déclassé avec les versions en commençant par le numéro de version 1.1.105.0 (publié février 2016). Cette section et la configuration doivent être n’est plus nécessaires, mais sont conservées sous forme de référence.

Pour l’authentification unique dans l’assistant pour l’utiliser, winhttp doit être configuré. Vous pouvez faire avec [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>L’assistant de connexion n’a pas été configurée correctement
Cette erreur s’affichent lorsque l’assistant de connexion ne peut pas atteindre le serveur proxy ou le proxy n’autorise pas la demande.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- Si vous voyez cela, observez la configuration du proxy dans [netsh](active-directory-aadconnect-prerequisites.md#connectivity) et vérifiez qu’elle est correcte.
![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- Si qui est à votre goût, suivez les étapes de [vérifier la connectivité proxy](#verify-proxy-connectivity) pour voir si le problème se produit à l’extérieur ainsi que l’Assistant.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
