<properties
    pageTitle="La configuration d’accès au conditionnelle locales avec Azure Active Directory d’inscription pour appareils | Microsoft Azure"
    description="Guide étape par étape pour activer l’accès conditionnelle aux applications locales à l’aide du Service de fédération Active Directory (AD FS) dans Windows Server 2012 R2."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>


# <a name="setting-up-on-premises-conditional-access-using-azure-active-directory-device-registration"></a>La configuration d’accès au conditionnelle locales avec Azure Active Directory d’inscription pour appareils

Personnellement appartenant périphériques de vos utilisateurs peuvent être marqués connus pour votre organisation en nécessitant que les utilisateurs au lieu de travail jointure leurs appareils au service Azure Active Directory d’inscription pour appareils. Voici un guide étape par étape pour activer l’accès conditionnelle aux applications locales à l’aide du Service de fédération Active Directory (AD FS) dans Windows Server 2012 R2.

> [AZURE.NOTE]
> Licence Office 365 ou Azure AD Premium est nécessaire lors de l’utilisation de périphériques enregistrés dans les stratégies d’accès conditionnelle service Azure Active Directory d’inscription pour appareils. Cela inclut les stratégies appliqués par les Services de fédération Active Directory (AD FS) vers des ressources locales.

Pour plus d’informations sur les scénarios d’accès conditionnel pour local, voir [joindre au lieu de travail à partir de n’importe quel appareil pour l’authentification unique et transparente deuxième facteur d’authentification société entre des Applications](https://technet.microsoft.com/library/dn280945.aspx).

Ces fonctionnalités sont disponibles pour les clients qui acheter une licence Azure Active Directory Premium.

<a name="supported-devices"></a>Appareils pris en charge
-------------------------------------------------------------------------
* Appareils joint au domaine Windows 7.
* Windows 8.1 personnel et domaine rejoint appareils.
* iOS 6 et versions ultérieures, pour le navigateur Safari
* Android 4.0 ou version ultérieure, Samsung GS3 ou au-dessus de téléphones, Samsung Note2 ou au-dessus de tablettes.


<a name="scenario-prerequisites"></a>Conditions préalables de scénario
------------------------------------------------------------------------
* Abonnement à Office 365 ou Premium Azure Active Directory
* Un client Azure Active Directory
* Active Directory Windows Server (Windows Server 2008 ou version ultérieure)
* Schéma mis à jour dans Windows Server 2012 R2
* Licence pour Premium Azure Active Directory
* Windows Server 2012 R2 Federation Services, configuré pour l’authentification unique à Azure Active Directory
* Windows Server 2012 R2 Web Application Proxy Microsoft Azure Active Directory se connecter (Azure AD Connect). [Télécharger Azure AD Connect ici](http://www.microsoft.com/en-us/download/details.aspx?id=47594).
* Domaine vérifié.

<a name="known-issues-in-this-release"></a>Problèmes connus dans cette version
-------------------------------------------------------------------------------
* Stratégies d’accès conditionnelle en fonction de périphérique nécessitent écriture différée appareil objet à Active Directory à partir d’Azure Active Directory. Cela peut prendre jusqu'à 3 heures pour les objets appareil soit précédent écrit à Active Directory
* les appareils iOS 7 vous invitera toujours l’utilisateur à sélectionner un certificat lors de l’authentification de certificat client.
* Certaines versions d’iOS8, avant d’iOS 8.3 ne fonctionnent pas.

## <a name="scenario-assumptions"></a>Hypothèses de scénario
Ce scénario part du principe que vous disposez d’un environnement hybride composée d’un client Azure AD et un locale d’Active Directory. Ces clients doivent être connectés à l’aide d’Azure AD Connect et avec un domaine vérifié et AD FS pour l’authentification unique. L’aide-mémoire ci-dessous vous aideront à configurer votre environnement à l’étape ci-dessus.

<a name="checklist-prerequisites-for-conditional-access-scenario"></a>Liste de vérification : Conditions préalables pour scénario d’accès conditionnel
--------------------------------------------------------------
Se connecter à votre client Azure AD avec Active Directory local.

## <a name="configure-azure-active-directory-device-registration-service"></a>Configurer le Service de l’inscription d’appareil Azure Active Directory
Utilisez ce guide pour déployer et configurer le Service Azure Active Directory appareil d’enregistrement pour votre organisation.

Ce guide suppose que vous avez configuré Windows Server Active Directory et que vous avez souscrit à Microsoft Azure Active Directory. Voir conditions préalables ci-dessus.

Pour déployer le Service Azure Active Directory appareil d’inscription avec votre client Azure Active Directory, effectuez les tâches dans la liste de contrôle suivant dans l’ordre. Un lien de référence vous permet d’accéder à une rubrique conceptuelle, revenez à cette liste de vérification après avoir examiné la rubrique conceptuelle afin que vous pouvez effectuer les tâches restantes dans cette liste de vérification. Certaines tâches inclura une étape de validation de scénario qui peut vous aider à confirmer que l’étape a réussi.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Partie 1 : Activer d’inscription pour appareils Azure Active Directory

Suivez l’aide-mémoire ci-dessous pour activer et configurer le Service d’enregistrement du périphérique Azure Active Directory.

| Tâche                                                                                                                                                                                                                                                                                                                                                                                             | Référence                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Activer l’enregistrement de dispositifs dans votre client Azure Active Directory pour permettre les périphériques à rejoindre l’espace de travail. Par défaut, l’authentification multifacteur n’est pas activée pour le service. Toutefois, l’authentification multifacteur est recommandée lorsque vous enregistrez un appareil. Avant d’activer l’authentification multifacteur dans ADRS, assurez-vous que AD FS est configuré pour un fournisseur de l’authentification multifacteur. | [Activer l’enregistrement appareil Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)               |
| Appareils découvre votre Service d’inscription Azure Active Directory appareil en recherchant des enregistrements DNS connus. Vous devez configurer votre société DNS permettant aux périphériques de découvrir votre Service d’inscription de Azure Active Directory appareils.                                                                                                                                                   | [Configurer la découverte Azure Active Directory d’inscription pour appareils.](active-directory-conditional-access-device-registration-overview.md) |

##<a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>2e partie : Déployer et configurer Windows Server 2012 R2 Active Directory Federation Services et définir un lien de la fédération avec Azure Active Directory

| Tâche                                                                                                                                                                                                                                                                                                                                                                                             | Référence                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Déploiement de domaine Active Directory Domain Services avec les extensions de schéma Windows Server 2012 R2. Vous n’avez pas besoin à tout votre contrôleur de domaine mise à niveau vers Windows Server 2012 R2. La mise à niveau de schéma est le seul impératif. | [Mettre à niveau votre schéma de Services de domaine Active Directory](#upgrade-your-active-directory-domain-services-schema)               |
| Appareils découvre votre Service d’inscription Azure Active Directory appareil en recherchant des enregistrements DNS connus. Vous devez configurer votre société DNS permettant aux périphériques de découvrir votre Service d’inscription de Azure Active Directory appareils.                                                                                                                                                   | [Préparer vos périphériques de prise en charge Active Directory](#prepare-your-active-directory-to-support-devices) |


##<a name="part-3-enable-device-writeback-in-azure-ad"></a>3e partie : Activer appareil écriture différée dans Azure Active Directory

| Tâche                                                                                                                                                                                                                                                                                                                                                                                             | Référence                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Terminer la partie 2 d’activation de l’écriture différée de périphérique dans Azure AD Connect. Une fois terminée, retournez cette ce guide. | [L’activation de l’appareil écriture différée dans Azure AD Connect](#upgrade-your-active-directory-domain-services-schema)               |


##<a name="optional-part-4-enable-multi-factor-authentication"></a>[Facultatif] 4e partie : Activer une authentification multifacteur

Il est fortement recommandé de configurer une des options différentes pour l’authentification multifacteur. Si vous souhaitez exiger l’authentification Multifacteur, voir [Choisir la solution de sécurité multifacteur pour vous](../multi-factor-authentication/multi-factor-authentication-get-started.md). Il inclut une description de chaque solution et les liens pour vous aider à configurer la solution de votre choix.

## <a name="part-5-verification"></a>Partie 5 : vérification

Le déploiement est terminé. Vous pouvez désormais essayer certains scénarios. Suivez les liens ci-dessous pour vous familiariser avec les fonctionnalités de se familiariser avec le service


| Tâche                                                                                                                                                                                                                         | Référence                                                                       |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| Participer à certains appareils à votre espace de travail à l’aide d’Azure Active Directory d’inscription pour appareils. Vous pouvez rejoindre, Windows, appareils iOS et Android                                                                                         | [Participer à des appareils mobiles sur votre espace de travail à l’aide d’Azure Active Directory d’inscription pour appareils](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Vous pouvez afficher et activer/désactiver les appareils inscrits à l’aide du portail d’administrateur. Dans cette tâche, vous allez afficher certains appareils enregistrées à l’aide du portail d’administrateur.                                                        | [Vue d’ensemble de l’inscription de périphérique Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)                             |
| Vérifiez que les objets du périphérique sont réécrits à partir d’Azure Active Directory sur Windows Server Active Directory.                                                                                                                  | [Vérifiez les appareils inscrits sont précédent écrites dans Active Directory](#verify-registered-devices-are-written-back-to-active-directory)                  |
| Maintenant que les utilisateurs peuvent enregistrer leurs périphériques, vous pouvez créer application stratégies d’accès dans AD FS qui permettent uniquement les appareils inscrits. Dans cette tâche, vous allez créer une règle d’application access et personnalisé accéder message refusée | [Créer une stratégie d’application access et personnalisé message accès refusé](#create-an-application-access-policy-and-custom-access-denied-message)            |



## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Intégrer Azure Active Directory avec Active Directory local
Cela vous aidera à intégrer votre client Azure Active Directory local active directory, à l’aide d’Azure AD Connect. Bien que les étapes sont disponibles dans le portail classique Azure, prenez note de toutes les instructions indiquées répertoriés dans cette section.

1.  Connectez-vous au portail classique Azure à l’aide d’un compte qui est un administrateur Global dans Azure Active Directory.
2.  Dans le volet gauche, sélectionnez **Active Directory**.
3.  Sous l’onglet **annuaire** , sélectionnez votre répertoire.
4.  Sélectionnez l’onglet **Intégration d’annuaire** .
5.  Sous la section **déployer et gérer** , suivez les étapes 1 à 3 pour intégrer Azure Active Directory dans l’annuaire local.
  1.    Ajouter des domaines.
  2.    Installer et exécuter Azure AD Connect : installer Azure AD Connect en suivant les instructions suivantes, [installation personnalisée de Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).
  3. Vérifier et gérer la synchronisation d’annuaires. Instructions d’authentification unique sont disponibles dans cette étape.

  > [AZURE.NOTE]
  > Configurer la fédération avec AD FS, comme indiqué dans le document lié ci-dessus. Vous n’avez pas besoin configurer toutes les fonctionnalités d’aperçu.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Mettre à niveau votre schéma Active Directory Domain Services

> [AZURE.NOTE]
> La mise à niveau votre schéma Active Directory ne peut pas être annulée. Il est recommandé de tout d’abord effectuer ceci dans un environnement de test.

1. Connectez-vous à votre contrôleur de domaine avec un compte qui dispose des droits d’administrateur de l’entreprise et administrateurs du schéma.
2. Copier le répertoire **\support\adprep [media]** et sous-répertoires un Active Directory contrôleur de domaine.
3. Où [media] est le chemin d’accès au support d’installation Windows Server 2012 R2.
4. À partir d’une invite de commandes, accédez au répertoire adprep et exécutez : **adprep.exe /forestprep**. Suivez les instructions à l’écran pour terminer la mise à niveau de schéma.

## <a name="prepare-your-active-directory-to-support-devices"></a>Préparer votre Active Directory pour prendre en charge des appareils mobiles

>[AZURE.NOTE] Il s’agit d’une opération unique que vous devez exécuter pour préparer votre forêt Active Directory pour prendre en charge des appareils mobiles. Vous devez être connecté avec des autorisations d’administrateur d’entreprise et votre forêt Active Directory doit avoir le schéma de Windows Server 2012 R2 pour effectuer cette procédure.


##<a name="prepare-your-active-directory-forest-to-support-devices"></a>Préparer votre forêt Active Directory pour prendre en charge des appareils mobiles

> [AZURE.NOTE]
>Il s’agit d’une opération unique que vous devez exécuter pour préparer votre forêt Active Directory pour prendre en charge des appareils mobiles. Vous devez être connecté avec des autorisations d’administrateur d’entreprise et votre forêt Active Directory doit avoir le schéma de Windows Server 2012 R2 pour effectuer cette procédure.

### <a name="prepare-your-active-directory-forest"></a>Préparer votre forêt Active Directory

1.  Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell et tapez : initialisation ADDeviceRegistration
2.  Lorsque vous y êtes invité pour **ServiceAccountName**, entrez le nom du compte de service que vous avez sélectionné comme compte de service pour AD FS. S’il s’agit d’un compte gMSA, entrez le compte dans le format **forme domaine\nom_compte€** . Pour un compte de domaine, utilisez le format **forme domaine\nom_compte**.



### <a name="enable-device-authentication-in-ad-fs"></a>Activer l’authentification appareil dans AD FS

1. Sur votre serveur de fédération, ouvrez la console de gestion AD FS et accédez à **AD FS** > **Stratégies d’authentification**.
2. Sélectionnez **Modifier Global d’authentification principal...** dans le volet **Actions** .
3. Cochez **Activer l’authentification de l’appareil** , puis sélectionnez**OK**.
4. Par défaut, AD FS supprime régulièrement appareils inutilisées depuis Active Directory. Vous devez désactiver cette tâche lorsque vous utilisez Azure Active Directory d’inscription pour appareils afin que les périphériques peuvent être gérés dans Azure.


### <a name="disable-unused-device-cleanup"></a>Désactiver le nettoyage du périphérique inutilisées
1. Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell et tapez : Set-AdfsDeviceRegistration - MaximumInactiveDays 0

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Préparer Azure AD Connect pour l’écriture différée appareil

1.  Terminer la partie 1 : Préparer Azure AD se connecter.


## <a name="join-devices-to-your-workplace-using-azure-active-directory-device-registration"></a>Participer à des appareils mobiles sur votre espace de travail à l’aide d’Azure Active Directory d’inscription pour appareils

### <a name="join-an-ios-device-using-azure-active-directory-device-registration"></a>Participer à un appareil iOS à l’aide d’Azure Active Directory d’inscription pour appareils

Azure Active Directory d’inscription appareils mobiles utilise le processus d’inscription Over-the-Air profil pour les appareils iOS. Ce processus commence par l’utilisateur qui se connecte à l’URL de l’inscription de profil à l’aide d’un navigateur web Safari. Le format de l’URL est la suivante :

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Où `yourdomainname` est le nom de domaine que vous avez configuré avec Azure Active Directory. Par exemple, si votre nom de domaine est contoso.com, l’URL serait :

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Il existe différentes façons de communiquer cette URL à vos utilisateurs. Recommandée consiste à publier cette URL dans une application personnalisée accès refusé message dans AD FS. Cela est décrit dans la section à venir : [créer une stratégie d’application access et personnalisé message accès refusé](#create-an-application-access-policy-and-custom-access-denied-message).

###<a name="join-a-windows-81-device-using-azure-active-directory-device-registration"></a>Participer à un appareil Windows 8.1 à l’aide de Azure Active Directory d’inscription pour appareils

1. Sur votre appareil Windows 8.1, accédez aux **Paramètres du PC** > **réseau** > **Poste de travail**.
2. Entrez votre nom d’utilisateur au format UPN. Par exemple, dan@contoso.com..
3. Cliquez sur **joindre**.
4. Lorsque vous y êtes invité, connectez-vous avec vos informations d’identification. L’appareil est joint maintenant.

###<a name="join-a-windows-7-device-using-azure-active-directory-device-registration"></a>Participer à un appareil Windows 7 à l’aide de Azure Active Directory d’inscription pour appareils
Pour Windows 7 domaine enregistré jointes appareils que vous avez besoin pour déployer le package de logiciels d’inscription appareil. Le logiciel est appelé jointure poste de travail pour Windows 7 et est disponible au téléchargement sur le [site Web Microsoft Connect](https://connect.microsoft.com/site1164). Instructions comment utiliser le package sont disponibles dans la page [d’inscription automatique appareils mobiles configurer pour Windows 7 domaine rejoint appareils](active-directory-conditional-access-automatic-device-registration-windows7.md).

### <a name="join-an-android-device-using-azure-active-directory-device-registration"></a>Participer à un appareil Android à l’aide d’Azure Active Directory d’inscription pour appareils

L' [Authentificateur Azure pour la rubrique Android](active-directory-conditional-access-azure-authenticator-app.md) contient des instructions sur la façon d’installer authentificateur Azure application sur votre appareil Android et ajouter un compte professionnel. Lorsqu’un compte de travail est créé avec succès sur un appareil Android, cet appareil est joint à l’organisation un espace de travail.

## <a name="verify-registered-devices-are-written-back-to-active-directory"></a>Vérifiez les appareils inscrits sont précédent écrites dans Active Directory
Vous pouvez afficher et vérifier que vos objets appareil ont été écrites revenir à votre Active Directory à l’aide de LDP.exe ou ADSI Edit. Les deux sont disponibles avec les outils administrateurs Active Directory.

Par défaut, les objets périphériques précédent écrit à partir d’Azure Active Directory sont effectués dans le même domaine que votre batterie de serveurs AD FS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Créer une stratégie d’application access et personnalisé message accès refusé
Considérez le scénario suivant : vous créez une application de gestion de la confidentialité dans AD FS fête Relying et configurer une règle de l’autorisation d’émission qui autorise uniquement les périphériques enregistrés. Maintenant que les périphériques sont enregistrés sont autorisés à accéder à l’application. Pour faciliter leur pour vos utilisateurs d’accéder à l’application, vous configurez un accès personnalisé refusé message qui inclut des instructions sur la façon de participer à leur appareil. Vos utilisateurs ont désormais un moyen transparent d’enregistrer leurs appareils afin d’accéder à une application.

Les étapes suivantes vous montrent comment mettre en œuvre ce scénario.

>[AZURE.NOTE]
Cette section suppose que vous avez déjà configuré une approbation de fête vous appuyer pour votre application dans AD FS.

1. Ouvrez l’outil AD FS MMC et accédez à AD FS > relations d’approbation > approbations des parties de confiance.
2. Recherchez l’application pour laquelle s’applique cette nouvelle règle d’accès. Avec le bouton droit de l’application, puis sélectionnez Modifier les règles réclamer...
3. Sélectionnez l’onglet **Règles d’autorisation d’émission** , puis sélectionnez **Ajouter une règle**
4. À partir de la **règle réclamer** modèle liste déroulante, sélectionnez **Autoriser ou refuser les utilisateurs basé sur une demande entrante**. Cliquez sur **suivant**.
5. Dans le nom de la règle réclamer : type de champ : **Autoriser l’accès à partir d’appareils enregistrés**
6. À partir d’entrants revendiquer type : zone de liste déroulante, sélectionnez **Est un utilisateur inscrit**.
7. Dans l’entrant réclamer valeur : de champ, tapez : **true**
8. Sélectionnez la case **Autoriser l’accès aux utilisateurs disposant de cette demande entrante** .
9. Sélectionnez **Terminer** , puis sur **Appliquer**.
10. Supprimer toutes les règles sont plus permissives que la règle que vous venez de créer. Par exemple, supprimez la règle **Autoriser l’accès à tous les utilisateurs** par défaut.

Votre application est maintenant configurée pour autoriser l’accès uniquement lorsque l’utilisateur arrive à partir d’un périphérique qu’ils inscrit et joint à l’espace de travail. Pour les stratégies d’accès au plus avancées, voir [Gérer les risques grâce à un contrôle d’accès multifacteur](https://technet.microsoft.com/library/dn280949.aspx).

Ensuite, vous allez configurer un message d’erreur personnalisé pour votre application. Le message d’erreur permet aux utilisateurs de savoir qu’ils doivent relient leur appareil au poste de travail avant qu’ils sont autorisés à accéder à l’application. Vous pouvez créer un application personnalisée message accès refusé à l’aide personnalisée HTML et Windows PowerShell.

Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell, puis tapez la commande suivante. Remplacer des portions de la commande avec des éléments qui sont spécifiques à votre système :

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Vous devez enregistrer votre périphérique avant d’accéder à cette application.

**Si vous utilisez un appareil iOS, cliquez sur ce lien pour participer à votre appareil**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Participer à cet appareil iOS sur votre espace de travail.


**Si vous utilisez un appareil Windows 8.1**, vous pouvez rejoindre votre appareil en accédant à **Paramètres du PC ** >  **réseau** > **Poste de travail**.


Où «**lieu d’utiliser des nom d’approbation de partie**» est le nom de l’objet de confiance de fête lieu d’utiliser des applications dans AD FS.
Où **votredomaine.com** est le nom de domaine que vous avez configuré avec Azure Active Directory. Par exemple, contoso.com.
Veillez à supprimer tous les sauts de ligne (le cas échéant) à partir du contenu html que vous passez à l’applet de commande **Set-AdfsRelyingPartyWebContent** .


Maintenant lorsque les utilisateurs accèdent à votre application d’un appareil n’est pas enregistré avec le Service d’enregistrement du périphérique Azure Active Directory, ils reçoivent une page qui est semblable à la capture d’écran ci-dessous.

![Screeshot d’une erreur lorsque les utilisateurs n’ont pas enregistré leur appareil avec Azure AD](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

##<a name="related-articles"></a>Articles connexes

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
