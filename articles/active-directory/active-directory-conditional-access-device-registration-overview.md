<properties
    pageTitle="Vue d’ensemble d’inscription pour appareils Active Directory Azure | Microsoft Azure"
    description="sert de scénarios accès conditionnelle basée sur un appareil. Lorsqu’un appareil est inscrit, Azure Active Directory d’inscription pour appareils met l’appareil avec une identité qui est utilisée pour authentifier le périphérique lorsque l’utilisateur se connecte."
    services="active-directory"
    keywords="Enregistrer et périphériques d’inscription pour appareils activer, d’inscription appareils mobiles et la gestion des périphériques"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="Markvi"/>

# <a name="get-started-with-azure-active-directory-device-registration"></a>Prise en main Azure Active Directory d’inscription pour appareils

Azure Active Directory d’inscription appareils mobiles sert de scénarios accès conditionnelle basée sur un appareil. Lorsqu’un appareil est inscrit, Azure Active Directory d’inscription pour appareils fournit l’appareil avec une identité qui est utilisée pour authentifier le périphérique lorsque l’utilisateur se connecte. Le périphérique authentifié et des attributs de l’appareil, puis utilisable pour appliquer les stratégies d’accès conditionnel pour les applications qui sont hébergées dans le nuage et en local.

Lorsqu’il est combiné avec une solution management(MDM) appareil mobile tel que Microsoft Intune, les attributs du périphérique dans Azure Active Directory sont mises à jour des informations supplémentaires sur l’appareil. Permet de créer des règles d’accès conditionnel qui mettent en œuvre access à partir d’appareils pour répondre à vos normes de sécurité et de conformité. Pour plus d’informations sur l’inscription des appareils dans Microsoft Intune, voir [appareils inscrire pour la gestion des Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune).

## <a name="scenarios-enabled-by-azure-active-directory-device-registration"></a>Scénarios activées par Azure Active Directory d’inscription pour appareils

Prise en charge pour iOS, Android et Windows Azure Active Directory d’inscription appareils mobiles inclut les appareils mobiles. Scénarios individuels qui utilisent des fonctionnalités d’inscription Azure AD appareils mobiles peuvent avoir plus spécifiques exigences et plateforme prend en charge. Ces scénarios sont les suivants :

- **Accès conditionnel aux applications qui sont hébergés en local**: vous pouvez utiliser les appareils inscrits avec les stratégies d’accès pour les applications qui sont configurées pour utiliser AD FS avec Windows Server 2012 R2. Pour plus d’informations sur la configuration des accès conditionnel pour local, voir [comment configurer l’accès conditionnelle locales à l’aide d’Azure Active Directory d’inscription pour appareils](active-directory-conditional-access-on-premises-setup.md).

- **Accès conditionnel pour les applications Office 365 avec Microsoft Intune** : les administrateurs informatiques peuvent prévoir les stratégies d’appareil accès conditionnelle pour sécuriser les ressources d’entreprise, tout en autorisant travailleurs de l’information sur les appareils compatibles pour accéder aux services. Pour plus d’informations, voir [Conditionnelle appareil les stratégies d’accès pour les services Office 365](active-directory-conditional-access-device-policies.md).

##<a name="setting-up-azure-active-directory-device-registration"></a>La configuration d’Azure Active Directory d’inscription pour appareils

Vous devez activer l’inscription Azure AD appareil dans le portail Azure permettant aux appareils mobiles de découvrir le service en recherchant des enregistrements DNS connus. Vous devez configurer votre société DNS afin que Windows 10, Windows 8.1, Windows 7, Android et les appareils iOS pouvant découvrir et d’utiliser le service.
Vous pouvez afficher et activer/désactiver les appareils inscrits à l’aide du portail d’administrateur dans Azure Active Directory.

>[AZURE.NOTE]
 Pour voir dernières obtenir des instructions sur la configuration d’inscription automatique appareils mobiles, [configuration de l’enregistrement automatique du domaine Windows joint appareils avec Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

### <a name="enable-azure-active-directory-device-registration-service"></a>Activer le Service d’inscription appareil Azure Active Directory

1. Connectez-vous au portail Microsoft Azure en tant qu’administrateur.
2. Dans le volet gauche, sélectionnez **Active Directory**.
3. Sous l’onglet **annuaire** , sélectionnez votre répertoire.
4. Sélectionnez l’onglet **configurer** .
5. Faites défiler jusqu'à la section intitulée **appareils**.
6. Sélectionnez **tout** pour **les utilisateurs risquent de périphériques de jointure poste de travail**.
7. Sélectionnez le nombre maximal de périphériques que vous souhaitez autoriser par utilisateur.

>[AZURE.NOTE]
>L’inscription avec Microsoft Intune ou gestion des appareils mobiles pour Office 365 nécessite rejoindre d’espace de travail. Si vous avez configuré un de ces services, tout est sélectionné et le bouton NONE est désactivé.

Par défaut, l’authentification à deux facteurs n’est pas activée pour le service. Toutefois, l’authentification à deux facteurs est recommandée lorsque vous enregistrez un appareil.

- Avant de demande d’authentification à deux facteurs pour ce service, vous devez configurer un fournisseur d’authentification à deux facteurs dans Azure Active Directory et configurer vos comptes d’utilisateur pour l’authentification multifacteur, voir [Ajout de l’authentification multifacteur à Azure Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)

- Si vous utilisez AD FS avec Windows Server 2012 R2, vous devez configurer un module d’authentification à deux facteurs dans AD FS, voir [L’aide de l’authentification multifacteur avec Active Directory Federation Services](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="configure-azure-active-directory-device-registration-discovery"></a>Configurer la découverte Azure Active Directory d’inscription pour appareils
Appareils Windows 7 et Windows 8.1 découvre le service d’enregistrement de périphérique en combinant le nom de compte d’utilisateur avec un nom de serveur d’inscription pour appareils connu.

Vous devez créer un enregistrement DNS CNAME qui pointe vers l’enregistrement A associé à votre service Azure Active Directory d’inscription pour appareils. L’enregistrement CNAME doit utiliser l’enterpriseregistration préfixe connu suivie du suffixe UPN utilisé par les comptes d’utilisateurs dans votre organisation. Si votre organisation utilise plusieurs suffixes UPN, plusieurs enregistrements CNAME doivent être créés dans le système DNS.

Par exemple, si vous utilisez deux suffixes UPN à votre organisation nommée @contoso.com et @region.contoso.com, vous allez créer les enregistrements DNS suivants.

| Entrée                                     | Type  | Adresse                            |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com        | CNAME | enterpriseregistration.Windows.NET |
| enterpriseregistration.Region.contoso.com | CNAME | enterpriseregistration.Windows.NET |

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Afficher et gérer des objets de périphériques dans Azure Active Directory
1. À partir du portail Azure administrateur, vous pouvez afficher, bloquer et débloquer appareils. Un périphérique est bloqué n’auront plus accès aux applications qui sont configurés pour autoriser uniquement les appareils inscrits.
2. Connectez-vous au portail Microsoft Azure en tant qu’administrateur.
3. Dans le volet gauche, sélectionnez **Active Directory**.
4. Sélectionnez votre annuaire.
5. Sélectionnez l’onglet **utilisateurs** . Puis sélectionnez un utilisateur d’afficher leurs appareils
6. Sélectionnez l’onglet **périphériques** .
7. Sélectionnez **Les périphériques inscrits** dans le menu déroulant.
8. Vous pouvez afficher ici, bloquer ou débloquer les périphériques des utilisateurs.

## <a name="additional-topics"></a>Autres rubriques

Vous pouvez enregistrer vos appareils Windows 7 et Windows 8.1 domaine rejoint auprès d’inscription Azure AD appareils mobiles. Les rubriques suivantes fournit des informations supplémentaires sur les conditions préalables et les étapes nécessaires pour configurer l’enregistrement de dispositifs sur les appareils Windows 7 et Windows 8.1.

- [Enregistrement automatique appareil avec Azure Active Directory pour les appareils Windows à un domaine](active-directory-conditional-access-automatic-device-registration.md)
- [Configurer l’enregistrement du dispositif automatique pour appareils joint au domaine Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Configurer l’inscription automatique appareil pour les appareils Windows 8.1 joint au domaine](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Enregistrement de dispositif automatique avec des appareils à un domaine Azure Active Directory pour Windows 10](active-directory-azureadjoin-devices-group-policy.md)
