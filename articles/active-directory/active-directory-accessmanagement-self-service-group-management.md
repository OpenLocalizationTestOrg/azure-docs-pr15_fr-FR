<properties
    pageTitle="Configuration de la Azure Active Directory pour la gestion de l’accès libre service application | Microsoft Azure"
    description="Gestion de groupe libre-service permet aux utilisateurs de créer et gérer des groupes de sécurité ou des groupes Office 365 dans Azure Active Directory et offre aux utilisateurs la possibilité de groupe de sécurité demande ou appartenances à un groupe Office 365"
    services="active-directory"
    documentationCenter=""
  authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="setting-up-azure-active-directory-for-self-service-group-management"></a>La configuration d’Azure Active Directory pour la gestion des groupes libre-service

Gestion des groupes libre-service permet aux utilisateurs de créer et gérer des groupes de sécurité ou des groupes Office 365 dans Azure Active Directory (AD Azure). Les utilisateurs peuvent également demander appartenances à un groupe Office 365 ou groupe de sécurité et puis le propriétaire du groupe peut approuver ou refuser l’appartenance. De cette façon, au quotidien contrôle de l’appartenance aux groupes peut être délégué aux personnes qui comprennent le contexte professionnel pour cet abonnement. Fonctionnalités de gestion libre-service du groupe sont disponibles uniquement pour les groupes de sécurité et groupes Office 365, mais pas pour les groupes de sécurité à extension messagerie ou des listes de distribution.

Gestion des groupe libre-service actuellement comporte deux scénarios essentiels : déléguée groupe gestion et libre-service du groupe.

- **Déléguer la gestion de groupe** 
   un exemple est un administrateur qui est gestion de l’accès à une application de SaaS à l’aide de la société. Gestion de ces droits d’accès devient difficile, afin que celui-ci vous invite à indiquer le propriétaire d’entreprise pour créer un nouveau groupe. L’administrateur attribue des accès de l’application vers le nouveau groupe et l’ajoute au groupe toutes les personnes déjà accès à l’application. Le propriétaire d’entreprise peut ensuite ajouter d’autres utilisateurs, et ces utilisateurs sont automatiquement mis en service à l’application. Le propriétaire d’entreprise n’a pas besoin d’attendre que l’administrateur de gérer l’accès des utilisateurs. Si l’administrateur accorde l’autorisation même vers un responsable dans un groupe autre professionnel, cette personne peut également gérer leurs propre aux utilisateurs l’accès. Le Gestionnaire de ni le propriétaire d’entreprise peut l’afficher ou gérer les utilisateurs de l’autre. L’administrateur peut toujours afficher tous les utilisateurs qui ont accès à l’application et les droits d’accès bloc si nécessaire.

- **Gestion des groupes libre-service** 
   est un exemple de ce scénario deux utilisateurs autorisés à la fois des sites SharePoint Online ils configurent séparément. Qu’ils souhaitent donner accès aux équipes d’autre à leurs sites. Pour ce faire, ils peuvent créer un groupe dans Azure AD et dans SharePoint Online chacune d’elle sélectionne ce groupe pour accéder à leurs sites. Lorsqu’une personne souhaite accéder, demande de ces derniers dans le panneau d’accès et après l’approbation ils accédez aux deux sites SharePoint Online automatiquement. Une version ultérieure, un d’eux décide que toutes les personnes qui accèdent au site doivent également accéder à une application SaaS particulière. L’administrateur de l’application SaaS peut ajouter des droits d’accès de l’application sur le site SharePoint Online. Dès lors, toutes les demandes soient approuvés permet d’accéder aux deux sites SharePoint Online et également à cette application SaaS.

## <a name="making-a-group-available-for-end-user-self-service"></a>Mise à disposition un groupe pour l’utilisateur final libre-service

1. Dans le [portail classique Azure](https://manage.windowsazure.com), ouvrez votre annuaire Azure AD.

2. Sous l’onglet **configurer** , définissez la **gestion de groupe déléguée** sur activé.

3. Définir **les utilisateurs peuvent créer des groupes de sécurité** ou **les utilisateurs peuvent créer des groupes Office** sur activé.

Lorsque **les utilisateurs peuvent créer des groupes de sécurité** est activée, tous les utilisateurs dans votre annuaire sont autorisés à créer de nouveaux groupes de sécurité et ajouter des membres à ces groupes. Ces nouveaux groupes auraient également apparaissent dans le panneau d’accès pour tous les autres utilisateurs. Si le paramètre de stratégie dans le groupe le permet, d’autres utilisateurs peuvent créer des requêtes pour participer à ces groupes. Si **les utilisateurs peuvent créer des groupes de sécurité** est désactivée, les utilisateurs ne peuvent pas créer des groupes et ne peut pas modifier des groupes existants dont ils sont propriétaires. Toutefois, ils peuvent toujours gérer les appartenances à des groupes et approuver les demandes d’autres utilisateurs à rejoindre leurs groupes.

Vous pouvez également utiliser **les utilisateurs qui peuvent utiliser libre-service pour les groupes de sécurité** pour obtenir un contrôle d’accès plus précis sur la gestion des groupe libre-service pour vos utilisateurs. Lorsque **les utilisateurs peuvent créer des groupes** est activée, tous les utilisateurs dans votre annuaire sont autorisés à créer de nouveaux groupes et ajouter des membres à ces groupes. En définissant également **les utilisateurs qui peuvent utiliser libre-service pour les groupes de sécurité** à certaines, vous appliquez une restriction gestion uniquement à un groupe limité d’utilisateurs de groupe. Lorsque ce commutateur est défini sur certains, vous devez ajouter des utilisateurs au groupe SSGMSecurityGroupsUsers avant de pouvoir créer de nouveaux groupes et ajouter des membres. En définissant **les utilisateurs qui peuvent utiliser libre-service pour les groupes de sécurité** à tous, vous activez tous les utilisateurs dans votre annuaire pour créer de nouveaux groupes.

Vous pouvez également utiliser la zone de **groupe qui peut utiliser libre-service pour les groupes de sécurité** pour spécifier un nom personnalisé pour un groupe dont les membres peuvent utiliser les en libre service.

## <a name="additional-information"></a>Informations supplémentaires

Les articles suivants fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources des groupes de Azure Active Directory](active-directory-manage-groups.md)

* [Azure Active Directory applets de commande pour configurer les paramètres de groupe](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

* [Qu’est Azure Active Directory ?](active-directory-whatis.md)

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
