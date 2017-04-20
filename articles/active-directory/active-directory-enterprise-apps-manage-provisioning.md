<properties
    pageTitle="Utilisateur mise en service de gestion des applications d’entreprise dans l’aperçu Azure Active Directory | Microsoft Azure"
    description="Découvrez comment gérer le service de compte utilisateur pour les applications d’entreprise à l’aide de l’aperçu Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/12/2016"
    ms.author="asmalser"/>

#<a name="preview-managing-user-account-provisioning-for-enterprise-apps-in-the-new-azure-portal"></a>Aperçu : Gérer le compte d’utilisateur mis en service pour les applications d’entreprise dans le nouveau portail Azure

Cet article décrit comment utiliser le [portail Azure](https://portal.azure.com) pour gérer le compte d’utilisateur automatique mise en service et la désactivation des comptes pour les applications qui prennent en charge, notamment celles qui ont été ajoutés à partir de la catégorie « proposée » de la [Galerie d’applications Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Cette expérience de gestion dans le nouveau portail Azure est actuellement dans la version d’évaluation et cet article décrit les nouvelles fonctionnalités, ainsi que quelques limitations temporaires qui sont en place pendant la période d’aperçu. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md)

Pour en savoir plus sur la mise en service de compte automatique de l’utilisateur et son fonctionnement, consultez [automatiser la mise en service des utilisateurs et Deprovisioning aux Applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md).

##<a name="finding-your-apps-in-the-new-portal"></a>Recherche de vos applications dans le nouveau portail

À partir de septembre 2016, toutes les applications qui ont été configurées pour unique authentification dans un répertoire, par un administrateur d’annuaire à l’aide de la [Galerie d’applications Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) à l’intérieur du [portail classique Azure](https://manage.windowsazure.com), peuvent maintenant consulter et gérer dans le nouveau portail Azure.

Vous trouverez ces applications dans la section **Applications d’entreprise** du nouveau portail Azure sont accessibles via le menu **Autres Services** dans la zone de navigation gauche. Applications d’entreprise sont des applications qui ont été déployées et sont utilisées par les utilisateurs au sein de votre organisation.

![Carte d’Applications d’entreprise][0]

Cliquez sur le lien de **toutes les applications** sur la gauche affiche une liste de toutes les applications qui ont été configurés, y compris les applications qui avaient été ajoutées à partir de la galerie. Sélection d’une application du chargement de la carte de la ressource pour cette application, où les rapports peuvent être affichés pour cette application et une variété de paramètres pouvant être gérée.

Mise en service des paramètres de compte d’utilisateur peut être géré en sélectionnant **Provisioning** sur la gauche.

![Carte de ressources d’application][1]


##<a name="provisioning-modes"></a>Modes de mise en service

La carte **Provisioning** commence par un menu **en Mode** qui affiche les modes de mise en service sont pris en charge pour une application d’entreprise et leur permet d’être configurés. Les options disponibles incluent :

* **Automatique** - cette option s’affiche si Azure AD prend en charge automatique en fonction de l’API de mise en service et/ou la mise en service des comptes d’utilisateurs à cette application. La sélection de ce mode affiche une interface qui guide les administrateurs via configuration Azure AD pour vous connecter à l’API de gestion des utilisateurs de l’application, la création de flux de travail qui définissent comment doivent flux de données de compte d’utilisateur entre Azure AD et les mappages de compte et l’application et gérer l’annonce Azure mise en service de service.

* **Manuel** - cette option est affichée si Azure AD ne prend pas en charge la mise en service automatique des comptes d’utilisateurs à cette application. Cette option signifie que les enregistrements compte utilisateur stockées dans l’application doivent être gérés à l’aide d’un processus externe, selon les fonctionnalités de gestion et de mise en service utilisateur fournies par cette application (ce qui peut inclure la mise en service Just SAML).


##<a name="configuring-automatic-user-account-provisioning"></a>Configuration de mise en service de compte automatique de l’utilisateur

Sélection de l’option **automatique** affiche un écran est divisé en quatre sections :

###<a name="admin-credentials"></a>Informations d’identification d’administration
Il s’agit de l’endroit où les informations d’identification requises pour Azure AD pour vous connecter à la gestion des utilisateurs de l’application API sont entrés. L’entrée obligatoire varie en fonction de l’application. Pour en savoir plus sur les types d’informations d’identification et les exigences pour les applications spécifiques, consultez le [didacticiel de configuration pour cette application spécifique](active-directory-saas-app-provisioning.md#list-of-apps-that-support-automated-user-provisioning).

Cliquez sur le bouton **Tester la connexion** permet de vous permettent de tester les informations d’identification en demandant Azure tentative AD pour vous connecter à l’application de mise en service application utilisant les informations d’identification.

###<a name="mappings"></a>Mappages
Il s’agit où les administrateurs peuvent afficher et modifier le flux des attributs utilisateur entre Azure AD et l’application cible, mise à jour ou de sa mise en service des comptes d’utilisateur.

Il existe un jeu de mappages entre les objets d’utilisateur Azure Active Directory et de chaque application SaaS utilisateur préconfiguré. Certaines applications gérer d’autres types d’objets, tels que des groupes ou des Contacts. Sélection un de ces mappages dans le tableau indique l’éditeur de mappage vers la droite, où qu’ils peuvent être affichées et personnalisés.

![Carte de ressources d’application][2]

Personnalisations prises en charge lors de la première visualisation sont les suivantes :

* Activation et désactivation des mappages pour des objets spécifiques, tels que l’objet utilisateur Azure AD pour l’objet utilisateur de l’application SaaS.

* Modifier les attributs de flux à partir de l’objet utilisateur Azure AD pour l’objet utilisateur de l’application. Pour plus d’informations sur le mappage des attributs, voir [Présentation des types de mappage attribut](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).

* Filtrer les actions de mise à disponibilité Azure AD doit effectuer dans l’application cible, qui est une nouvelle fonctionnalité dans le portail Azure. Plutôt que de laisser Azure AD entièrement-synchroniser des objets, vous pouvez limiter les actions exécutées. Par exemple, en uniquement en sélectionnant **mise à jour**, Azure AD uniquement mises à jour utilisateur existant comptes dans une application et ne crée pas de nouveaux. En sélectionnant uniquement **créer**, Azure uniquement crée de nouveaux comptes d’utilisateur, mais ne met pas à jour existants. Cette fonctionnalité permet aux administrateurs de créer des mappages de différentes pour la création de compte et mettre à jour de flux de travail. La possibilité de créer plusieurs mappages par application complète est planifiée pour plus loin dans la période d’aperçu.

###<a name="settings"></a>Paramètres
Cette section permet aux administrateurs démarrer et arrêter l’annonce Azure mise en service de service pour l’application sélectionnée, ainsi que vous pouvez également vider le cache de mise en service et redémarrez le service.

Si la mise en service est activée pour la première fois pour une application, activer le service en modifiant l' **État de mise en service** sur **activé**. Cette provoque l’annonce Azure mise en service service pour effectuer une synchronisation initiale, où il lit les utilisateurs affectés dans la section **utilisateurs et groupes** , l’application cible pour les des requêtes et puis effectue les actions de mise à disponibilité définies dans la section Azure AD **mappages** . Pendant ce processus, le service de mise à disponibilité stocke des données mises en cache sur les comptes d’utilisateurs qu’il gère, sorte comptes non géré dans les applications cibles qui ont été jamais dans le cadre d’affectation ne sont pas affectés par la désactivation des comptes opérations. Après la synchronisation initiale, le service de mise à disponibilité synchronise automatiquement utilisateur et des objets de groupe sur un intervalle de dix minutes.

Modification de l' **État de mise en service** pour **désactiver** interrompt simplement le service de mise en service. Dans cet état, Azure ne pas créer, mettre à jour ou supprimer des objets de groupe dans l’application. Modification de l’état sur activé, le service choisir où elle s’était arrêtée.

Sélection de la case à cocher **Effacer état actuel et redémarrer la synchronisation** et l’enregistrement s’arrête le service de mise à disponibilité, vidage les données mises en cache sur les comptes Azure AD gère, redémarre les services et exécute la synchronisation initiale à nouveau. Cette option permet aux administrateurs démarrer le processus de déploiement mise en service de nouveau.

###<a name="synchronization-details"></a>Détails de synchronisation
Cette section fournit des détails addition sur le fonctionnement du service mise à disponibilité, y compris les heures prénom et que le service d’approvisionnement exécuté l’application et combien objets utilisateurs et groupes sont gérées.

Des liens sont fournies au **rapport d’activité Provisioning**, qui fournit un journal de tous les utilisateurs et groupes créé, mis à jour et supprimée entre Azure AD et la cible application et le **rapport d’erreurs Provisioning** qui fournit plus détaillée des messages d’erreur pour l’utilisateur et des objets de groupe qui n’a pas pu être lus, créé, mis à jour ou supprimé. 

[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
