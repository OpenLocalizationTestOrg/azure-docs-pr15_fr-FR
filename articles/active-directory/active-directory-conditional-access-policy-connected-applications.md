<properties
    pageTitle="Définir la stratégie d’accès conditionnelle basée sur un appareil pour applications connectées Azure Active Directory | Microsoft Azure"
    description="Définir les stratégies d’accès conditionnelle basée sur un appareil pour les applications Azure connecté AD."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="markvi"/>


# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>Définir la stratégie d’accès conditionnelle basée sur un appareil pour applications connectées Azure Active Directory


Accès Azure Active Directory (AD Azure) basé sur un appareil conditionnelle peut vous aider à protéger les ressources d’entreprise à partir de :

- Tentatives d’accès à partir d’appareils inconnus ou non gérés.
- Appareils mobiles qui ne respectent pas les stratégies de sécurité de votre organisation.

Pour une vue d’ensemble d’accès conditionnelle, voir [accéder conditionnelle Azure Active Directory](active-directory-conditional-access.md).

Vous pouvez définir les stratégies d’accès conditionnelle appareil pour protéger ces applications :

- Office 365 SharePoint Online, pour protéger des sites et des documents de votre organisation
- Office 365 Exchange Online, pour protéger la messagerie de votre organisation
- Logiciel comme une application de service (SaaS) qui sont connectés à Azure AD pour l’authentification
- Les applications qui sont publiés par l’utilisation des services Proxy d’Application Azure Active Directory local

Pour définir une stratégie d’accès conditionnelle basée sur un appareil, dans le portail Azure, accédez à l’application spécifique dans l’annuaire.


  ![Liste des applications dans le répertoire portail Azure] (./media/active-directory-conditional-access-policy-connected-applications/01.png "Applications")


Sélectionnez l’application, puis cliquez sur l’onglet **configurer** pour définir la stratégie d’accès conditionnelle.  


  ![Configurer l’application] (./media/active-directory-conditional-access-policy-connected-applications/02.png "Appareil en fonction des règles d’accès")




Pour définir une stratégie d’accès conditionnelle basée sur un appareil, dans la section **règles d’accès basés sur des périphériques** , **Activer les règles d’accès**, cliquez **sur**.

Une stratégie d’accès conditionnelle basée sur un appareil comporte trois composants :

- **Appliquer à**. L’étendue de la stratégie est appliquée à des utilisateurs.

- **Règles de périphériques**. Les conditions un appareil doit respecter avant d’accéder à l’application.

- **Mise en œuvre de l’application**. Les applications clientes (natif ou navigateur) la stratégie s’applique à.

  ![Les trois composants d’une stratégie d’accès basé sur un appareil] (./media/active-directory-conditional-access-policy-connected-applications/03.png "Appareil en fonction des règles d’accès")


## <a name="select-the-users-the-policy-applies-to"></a>Sélectionnez les utilisateurs d’à que la stratégie s’applique

Dans la section **Appliquer à** , vous pouvez sélectionner l’étendue de cette stratégie s’applique à des utilisateurs.

Vous avez deux options lorsque vous créez une étendue de stratégie d’accès pour les utilisateurs :

- **Tous les utilisateurs**. Appliquer la stratégie à tous les utilisateurs qui accèdent à l’application.

- **Groupes**. Limiter la stratégie aux utilisateurs un membre d’un groupe spécifique.

![Appliquer la stratégie à tous les utilisateurs ou à un groupe] (./media/active-directory-conditional-access-policy-connected-applications/11.png "Appliquer à")


 Pour exclure un utilisateur d’une stratégie, activez la case à cocher **à l’exception** . Cette opération est utile lorsque vous souhaitez accorder des autorisations à un utilisateur spécifique temporairement accéder à l’application. Sélectionnez cette option, par exemple, si certains de vos utilisateurs ont des périphériques qui ne sont pas prêts pour un accès conditionnel. Les appareils ne soit pas encore enregistrés, ou qu’ils doivent être se déconnecter de conformité.


## <a name="select-the-conditions-that-devices-must-meet"></a>Sélectionnez les conditions que doivent respecter les appareils mobiles

Utiliser des **Règles de périphériques** pour définir les conditions un appareil doivent respecter pour accéder à l’application.

Vous pouvez configurer des périphériques conditionnelle access à ces types de périphériques :

- Mise à jour de l’anniversaire de mariage Windows 10, Windows 8.1 et Windows 7
- Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2
- appareils iOS (iPhone, iPad)
- Appareils Android

Prise en charge pour Mac est bientôt disponible.

  ![Appliquer la stratégie aux appareils] (./media/active-directory-conditional-access-policy-connected-applications/04.png "Applications")

 >[AZURE.NOTE] Pour plus d’informations sur les différences entre les périphériques à un domaine et Azure AD-rejoint, voir les [périphériques à l’aide de Windows 10 dans votre espace de travail](active-directory-azureadjoin-windows10-devices.md).

Vous avez deux possibilités pour les règles de l’appareil :

- **Tous les périphériques doivent être conformes**. Toutes les plateformes d’appareil qui accèdent à l’application doivent être conformes. Appareils mobiles qui s’exécutent sur les plateformes qui ne prennent pas en charge les accès conditionnelle basée sur un appareil sont refusés.

- **Uniquement les périphériques sélectionnés doivent être conformes**. Uniquement les plateformes d’appareil spécifique doivent être conformes. D’autres plateformes ou autres plateformes qui peuvent accéder à l’application, ont accès.

  ![Définir l’étendue des règles de périphériques] (./media/active-directory-conditional-access-policy-connected-applications/05.png "Applications")

Appareils Azure AD-rejoint sont conformes s’ils sont marqués comme **conformes** dans l’annuaire par Intune ou un système de gestion d’appareil mobile tiers qui s’intègre à Azure AD.

Un appareil à un domaine est conforme si :

- Il est enregistré avec Azure AD. De nombreuses organisations considérer appareils à un domaine comme périphériques approuvés.
- Il est marqué comme étant **compatible avec** Azure AD par système Centre Gestionnaire de Configuration de 2016.

 ![Appareils à un domaine qui sont conformes] (./media/active-directory-conditional-access-policy-connected-applications/06.png "Règles de périphériques")

Périphériques personnels Windows sont conformes s’ils sont marqués comme **conformes** dans l’annuaire par Intune ou un système de gestion d’appareil mobile tiers qui s’intègre à Azure AD.

Appareils non Windows sont conformes s’ils sont marqués comme **conformes** dans l’annuaire par Intune.

 >[AZURE.NOTE] Pour plus d’informations sur la configuration Azure AD pour la conformité appareil dans des systèmes de gestion des différentes, voir [accéder conditionnelle Azure Active Directory](active-directory-conditional-access.md).


Vous pouvez sélectionner une ou plusieurs plateformes d’appareil pour une stratégie d’accès basé sur un appareil. Cela inclut Android, iOS, Windows Mobile (Windows 8.1 téléphones et tablettes) et Windows (tous les autres appareils Windows, y compris tous les appareils Windows 10).
Évaluation de la stratégie se produit uniquement sur les plateformes sélectionnés. Si un périphérique de tentatives d’accès n’est pas exécuté une des plateformes sélectionnées, l’appareil peut accéder à l’application si l’utilisateur a accès. Aucune stratégie de l’appareil n’est évaluée.

![Sélectionnez plateformes pour les règles de périphériques] (./media/active-directory-conditional-access-policy-connected-applications/07.png "Règles de périphériques")


## <a name="set-policy-evaluation-for-a-type-of-application"></a>Définir l’évaluation d’une stratégie pour un type d’application

Dans la section **Mise en œuvre de l’Application** , définissez le type d’applications évalue la stratégie d’utilisateur ou d’accès des appareils.

Vous avez deux possibilités pour le type d’application à inclure :

- Navigateur et applications natives
- Applications natives uniquement

![Choisir navigateur ou des applications natives] (./media/active-directory-conditional-access-policy-connected-applications/08.png "Applications")

Pour appliquer la stratégie d’accès pour les applications, sélectionnez **pour le navigateur et applications natives**. Ensuite, vous pouvez inclure :

- Navigateurs (par exemple, Microsoft Edge dans Windows 10) ou Safari dans iOS.
- Applications qui utilisent la bibliothèque terme (Active Directory authentification ADAL) sur n’importe quelle plate-forme, ou qui utilisent la WebAccountManager (WAM) API dans Windows 10.

>[AZURE.NOTE] Pour plus d’informations sur les navigateurs pris en charge et d’autres considérations pour un utilisateur disposant d’un accès appareil basée sur un, application protégées par autorité de certificat, voir [accéder conditionnelle Azure Active Directory](active-directory-conditional-access.md).

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>Protéger l’accès de messagerie à partir des applications Exchange ActiveSync

Dans les applications Office 365 Exchange Online, vous pouvez utiliser Exchange ActiveSync pour bloquer l’accès de messagerie aux applications de messagerie Exchange ActiveSync.

![Options de conformité Exchange ActiveSync] (./media/active-directory-conditional-access-policy-connected-applications/09.png "Applications")

![Exiger un périphérique d’accéder à messagerie compatible] (./media/active-directory-conditional-access-policy-connected-applications/10.png "Applications")


## <a name="next-steps"></a>Étapes suivantes

- [Accès à conditionnelle Azure Active Directory](active-directory-conditional-access.md)
