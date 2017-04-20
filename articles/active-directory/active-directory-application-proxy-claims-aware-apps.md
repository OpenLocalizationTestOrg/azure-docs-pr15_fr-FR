<properties
    pageTitle="Utilisation de revendications applications prenant en charge dans le Proxy d’Application"
    description="Décrit comment devenir opérationnel avec Azure AD Application Proxy."
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
    ms.date="06/22/2016"
    ms.author="kgremban"/>



# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Utilisation de revendications applications prenant en charge dans le Proxy d’Application

Applications prenant en charge revendications effectuer une redirection à la sécurité jeton STS (Service), qui à son tour les demandes d’informations d’identification de l’utilisateur en échange d’un jeton avant de rediriger l’utilisateur à l’application. Pour activer le Proxy d’Application pour l’utiliser avec ces redirections, les étapes suivantes doivent être prises.

## <a name="prerequisites"></a>Conditions préalables
Avant d’effectuer cette procédure, vérifiez que le STS l’application prenant en charge revendications redirige vers est disponible en dehors de votre réseau local.

## <a name="azure-classic-portal-configuration"></a>Configuration du portail classique Azure

1. Publiez votre application conformément aux instructions décrites dans les [applications de publication avec Proxy de l’Application](active-directory-application-proxy-publish.md).
2. Dans la liste des applications, sélectionnez l’application prenant en charge sur les revendications et cliquez sur **configurer**.
3. Si vous avez choisi **relais** en tant que votre **Méthode la pré-authentification**, veillez à sélectionner **HTTPS** en tant que votre modèle **d’URL externes** .
4. Si vous avez choisi **Azure Active Directory** en tant que votre **Méthode la pré-authentification**, sélectionnez **Aucun** comme votre **Méthode d’authentification interne**.


## <a name="adfs-configuration"></a>Configuration ADFS

1. Ouvrez l’outil Gestion ADFS.
2. Accédez au **Lieu d’utiliser des approbations des parties**, un clic droit sur l’application que vous publiez avec Proxy de l’Application, puis sélectionnez **Propriétés**.  
  ![Approbations des parties de confiance avec le bouton droit cliquez sur le nom de l’application - screentshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. Sous l’onglet **points de terminaison** , sous **type de point de terminaison**, sélectionnez **La fédération Web**.
4. Sous **URL approuvés** , entrez l’URL que vous avez entré dans le Proxy d’Application sous **URL externe** , puis cliquez sur **OK**.  
  ![Ajouter un point de terminaison - URL approuvés valeur - capture d’écran](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="see-also"></a>Voir aussi

- [Publier des applications avec le Proxy d’Application](active-directory-application-proxy-publish.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Résoudre les problèmes que vous rencontrez avec le Proxy d’Application](active-directory-application-proxy-troubleshoot.md)
- [Activer les applications native client interagir avec les applications de proxy](active-directory-application-proxy-native-client.md)

Pour les dernières nouvelles et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)
