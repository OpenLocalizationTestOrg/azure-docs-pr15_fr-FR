<properties
    pageTitle="Authentification unique avec le Proxy d’Application | Microsoft Azure"
    description="Explique comment fournir de l’authentification unique à l’aide de Proxy d’Application Azure AD."
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
    ms.date="08/10/2016"
    ms.author="kgremban"/>


# <a name="single-sign-on-with-application-proxy"></a>Authentification unique avec le Proxy d’Application

Authentification unique est un élément essentiel de Proxy d’Application Azure AD. Il fournit la meilleure expérience utilisateur les étapes suivantes :

1. Un utilisateur s’inscrit dans le cloud  
2. Tous les contrôles de sécurité s’exécutent dans le cloud (la pré-authentification)  
3. Lorsque la demande est envoyée à l’application local, l’Application Proxy Connector se substitue à l’utilisateur. L’application serveur principal pense qu’il s’agit d’un utilisateur régulier provenant d’un appareil à un domaine.

![Diagramme d’accès de l’utilisateur final, via un Proxy de l’Application, au réseau d’entreprise](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Azure AD Application Proxy vous permet d’obtenir une expérience d’authentification unique (SSO) pour vos utilisateurs. Utilisez les instructions suivantes pour publier vos applications à l’aide de l’authentification unique :


## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>Authentification unique pour les applications IWA local à l’aide de KCD avec le Proxy d’Application
Vous pouvez activer l’ouverture de session unique à vos applications à l’aide de l’authentification Windows (intégrée) en attribuant l’autorisation de l’Application Proxy connecteurs dans Active Directory pour prendre l’identité utilisateurs et envoyer et recevoir des jetons de leur nom.


### <a name="network-diagram"></a>Réseau de tâches

Ce diagramme explique le flux lorsqu’un utilisateur tente d’accéder à une application local qui utilise IWA.

![Diagramme de flux d’authentification Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. L’utilisateur entre l’URL pour accéder à l’application local via le Proxy d’Application.
2. Proxy de l’application redirige la demande aux services d’authentification Azure AD doit être pré-authentifiée. Azure AD s’applique à ce stade, aucune authentification applicable et les stratégies d’autorisation, telles que l’authentification multifacteur. Si l’utilisateur est validé, Azure AD crée un jeton et envoie à l’utilisateur.
3. L’utilisateur transfère le jeton au Proxy de l’Application.
4. Proxy de l’application valide le jeton et récupère le nom d’utilisateur Principal (UPN) et puis envoie la demande, le nom UPN et le nom (principaux Service) au connecteur via un canal sécurisé double authentifié.
5. Le connecteur effectue négociation Kerberos contrainte délégation (KCD) avec la local AD, emprunt d’identité de l’utilisateur pour obtenir un jeton de Kerberos à l’application.
6. Active Directory envoie le jeton Kerberos pour le lien de l’application.
7. Le connecteur envoie la demande d’origine au serveur d’applications, en utilisant le jeton Kerberos reçue annonce.
8. L’application envoie la réponse au connecteur qui est ensuite renvoyé au service Proxy de l’Application et enfin à l’utilisateur.

### <a name="prerequisites"></a>Conditions préalables

Avant de commencer avec l’authentification unique pour le Proxy d’Application, vérifiez que votre environnement est prêt avec les configurations et les paramètres suivants :

- Vos applications, telles que des applications SharePoint Web, sont configurées pour utiliser l’authentification Windows intégrée. Pour plus d’informations consultez [Activer la prise en charge pour l’authentification Kerberos](https://technet.microsoft.com/library/dd759186.aspx), ou pour SharePoint, voir [Planifier pour l’authentification Kerberos dans SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).

- Toutes vos applications portent des noms de Principal du Service.

- Le serveur qui exécute le connecteur et le serveur qui exécute l’application sont joint au domaine et partie du même domaine ou domaines autorisés à approuver. Pour plus d’informations sur la jointure de domaines, voir [participer à un ordinateur à un domaine](https://technet.microsoft.com/library/dd807102.aspx).

- Le serveur qui exécute le connecteur accède à lire le TokenGroupsGlobalAndUniversal des utilisateurs. Il s’agit d’un paramètre par défaut qui peut-être ont été affectée par sécurité renforcer l’environnement. Obtenir plus d’aide avec les marques dans [KB2009157](https://support.microsoft.com/en-us/kb/2009157).

### <a name="active-directory-configuration"></a>Configuration d’Active Directory

La configuration Active Directory varie selon que votre Application Proxy Connector et le serveur publié trouvent dans le même domaine ou non.

#### <a name="connector-and-published-server-in-the-same-domain"></a>Connecteur et serveur publié dans le même domaine

1. Dans Active Directory, accédez à **Outils** > **utilisateurs et ordinateurs**.
2. Sélectionnez le serveur qui exécute le connecteur.
3. Avec le bouton droit et sélectionnez **Propriétés** > **délégation**.
4. Sélectionnez **Approuver cet ordinateur pour la délégation aux services spécifiés** et sous **Services à laquelle ce compte peut présenter des informations d’identification déléguées**, ajoutez la valeur de l’identité de nom principal de service du serveur d’application.
5. Cela permet au connecteur de Proxy Application d’emprunt d’identité des utilisateurs dans AD contre les applications définies dans la liste.

![Capture d’écran de la fenêtre Propriétés du serveur de connecteur](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>Connecteur et serveur publié dans différents domaines

1. Pour une liste des conditions préalables pour l’utilisation de KCD sur plusieurs domaines, voir [La délégation contrainte Kerberos sur plusieurs domaines](https://technet.microsoft.com/library/hh831477.aspx).
2. Dans Windows 2012 R2, utilisez la `principalsallowedtodelegateto` propriété sur le serveur de connecteur pour activer le Proxy d’Application à déléguer pour le serveur de connecteur, où se trouve le serveur publié `sharepointserviceaccount` et le serveur délégation est `connectormachineaccount`.

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE] `sharepointserviceaccount`peut être le compte d’ordinateur SP ou un compte de service sous lequel s’exécute le pool d’applications SP.


### <a name="azure-classic-portal-configuration"></a>Configuration du portail classique Azure

1. Publiez votre application conformément aux instructions décrites dans les [applications de publication avec Proxy de l’Application](active-directory-application-proxy-publish.md). Veillez à sélectionner **Azure Active Directory** en tant que la **Méthode la pré-authentification**.
2. Une fois que votre application apparaît dans la liste des applications, sélectionnez-le, puis cliquez sur **configurer**.
3. Sous **Propriétés**, définissez **Interne méthode d’authentification** pour **L’authentification Windows intégrée**.  
  ![Configuration de l’Application avancées](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Entrez le **Nom principal de service Application interne** du serveur d’application. Dans cet exemple, le nom principal de service notre application publiée est http/lob.contoso.com.  

>[AZURE.IMPORTANT] Si votre UPN en local et le nom UPN dans Azure Active Directory ne sont pas identiques, vous devrez configurer la [délégation d’identité de connexion](#delegated-login-identity) dans l’ordre pour une Authentication préalable pour l’utiliser.

|  |  |
| --- | --- |
| Méthode d’authentification interne | Si vous utilisez Azure AD pour une Authentication préalable, vous pouvez définir une méthode d’authentification interne pour permettre à vos utilisateurs bénéficier d’authentification unique (SSO) pour cette application. <br><br> Sélectionnez **L’authentification Windows intégrée** (IWA) si votre application utilise IWA et vous pouvez configurer la délégation de contrainte de Kerberos (KCD) pour permettre l’authentification unique pour cette application. Les applications qui utilisent IWA doivent être configurées à l’aide de KCD, dans le cas contraire Proxy d’Application ne seront pas en mesure de publier ces applications. <br><br> Si votre application n’utilise pas IWA, sélectionnez **Aucun** . |
| Application interne nom principal de service | Il s’agit du nom de Service Principal (nom principal de service) de l’application interne tel que configuré dans le local Azure AD. Le nom principal de service est utilisé par le connecteur Proxy Application pour récupérer des jetons Kerberos pour l’application à l’aide de KCD. |


## <a name="sso-for-non-windows-apps"></a>Authentification unique pour les applications non Windows
Le flux de délégation Kerberos dans Azure AD Application Proxy démarre Azure AD authentifie l’utilisateur dans le cloud. Une fois que la demande arrive en local, Azure AD Application Proxy Connector problèmes d’un tickets Kerberos au nom de l’utilisateur en interaction avec l’annuaire Active Directory local. Ce processus est appelé en tant que Kerberos contrainte délégation (KCD). Dans la phase suivante, une demande est envoyée à l’application serveur principal avec cette tickets Kerberos. Il existe un certain nombre de protocoles qui définissent l’envoi de ces demandes. La plupart des serveurs non Windows attendre négociation/SPNego est désormais pris en charge par le Proxy d’Application Azure AD.

![Diagramme de l’authentification unique non Windows](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### <a name="delegated-login-identity"></a>Identité de connexion déléguée
Identité de connexion déléguée permet de gérer les deux scénarios de nom d’utilisateur différent :

- Applications non Windows qui généralement obtenir identité utilisateur sous la forme d’un nom d’utilisateur ou le nom du compte SAM, pas une adresse de messagerie (username@domain).
- Configurations de la connexion de remplacement dans laquelle le nom UPN dans Azure Active Directory et le nom UPN dans vos locaux Active Directory sont différents.

Proxy de l’Application, vous pouvez sélectionner quelle identité permet d’obtenir les tickets Kerberos. Ce paramètre est par application. Certaines de ces options ne conviennent pas pour les systèmes qui n’acceptent pas le format d’adresse de messagerie, d’autres personnes sont conçus pour la connexion de remplacement.

![Capture d’écran de connexion déléguée identité paramètre](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Si l’identité de connexion déléguée est utilisée, la valeur ne peut-être unique pour tous les domaines ou forêts dans votre organisation. Vous pouvez éviter ce problème en le publiant ces applications à l’aide des deux différents groupes connecteur. Dans la mesure où chaque application possède une audience utilisateur différent, vous pouvez joindre ses connecteurs vers un autre domaine.


## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>Utilisation de l’authentification unique lorsque locaux et cloud identités ne sont pas identiques.
À moins d’avoir configuré dans le cas contraire, le Proxy d’Application part du principe que les utilisateurs ont exactement la même identité dans le cloud et en local. Vous pouvez configurer, pour chaque application, quelle identité doit être utilisée lors de l’exécution de l’authentification unique.  

Cette fonctionnalité permet de nombreuses organisations ayant différentes identités local et le cloud pour que l’authentification unique à partir du cloud aux applications local sans nécessitant que les utilisateurs d’entrer les mots de passe et différents noms d’utilisateur. Cela inclut les organisations qui :

- Utilise plusieurs domaines en interne (joe@us.contoso.com, joe@eu.contoso.com) et un domaine uniques dans le nuage (joe@contoso.com).

- Disposez nom de domaine non prenant en interne (joe@contoso.usa) et une valeur juridique dans le cloud.

- N’utilisez pas de noms de domaine en interne (Jean)

- Utiliser des alias différents local et dans le cloud. Par exemple joe-johns@contoso.comVS.joej@contoso.com  

Il permettra également avec les applications qui n’acceptent pas les adresses sous la forme d’adresse de messagerie, qui est très fréquent que des serveurs non Windows principaux.


### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>Configuration de l’authentification unique pour différentes identités cloud et local

1. Configurer les paramètres de Azure AD Connect afin que l’identité principale sera l’adresse de messagerie (messagerie). Cela dans le cadre du processus de personnaliser, en modifiant le champ **Nom d’utilisateur principe** dans les paramètres de synchronisation. Notez que ces paramètres également déterminent comment les utilisateurs se connecter à Office 365, Windows10 périphériques et d’autres applications qui utilisent Azure AD leur banque d’identité.  
  ![Capture d’écran Identification utilisateurs - liste déroulante nom d’utilisateur Principal](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Dans les paramètres de Configuration de l’Application pour l’application que vous souhaitez modifier, sélectionnez l' **Identité de connexion déléguée** à utiliser :
  - Nom d’utilisateur principe :joe@contoso.com  
  - Autre principe nom d’utilisateur :joed@contoso.local  
  - Partie du nom d’utilisateur principe du nom d’utilisateur : Jean  
  - Partie du nom d’utilisateur autre principe du nom d’utilisateur : joed  
  - Nom du compte local SAM : configuration en fonction du contrôleur de domaine local

  ![Capture d’écran du menu déroulant login déléguée identité](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>Résolution des problèmes de l’authentification unique pour les différentes identités
S’il existe une erreur dans le processus de l’authentification unique s’affichent dans le journal des événements connecteur machine comme expliqué dans [résolution des problèmes](active-directory-application-proxy-troubleshoot.md).
Toutefois, dans certains cas, la demande est correctement envoyée à l’application serveur principal pendant cette application répondra dans différentes des autres réponses HTTP. Résolution des problèmes de ces cas doivent commencer par examiner le numéro d’événement 24029 sur l’ordinateur connecteur dans le journal des événements session Proxy de l’Application. L’identité de l’utilisateur qui a été utilisée pour la délégation s’affichent dans le champ « utilisateurs » dans les détails des événements. Pour activer le journal de session, sélectionnez **Afficher analytique et journaux de débogage** dans la visionneuse vue menu événements.


## <a name="see-also"></a>Voir aussi

- [Publier des applications avec le Proxy d’Application](active-directory-application-proxy-publish.md)
- [Résoudre les problèmes que vous rencontrez avec le Proxy d’Application](active-directory-application-proxy-troubleshoot.md)
- [Utilisation des applications prenant en charge sur les revendications](active-directory-application-proxy-claims-aware-apps.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)

Pour les dernières nouvelles et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg
