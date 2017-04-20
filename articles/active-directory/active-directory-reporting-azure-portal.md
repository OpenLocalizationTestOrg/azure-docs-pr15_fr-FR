<properties
   pageTitle="Azure Active Directory reporting - aperçu | Microsoft Azure"
   description="Répertorie les différents rapports disponibles pour preview Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="markusvi"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/30/2016"
   ms.author="markvi"/>

# <a name="azure-active-directory-reporting---preview"></a>Azure Active Directory reporting - aperçu

> [AZURE.SELECTOR]
- [Portail Azure](active-directory-reporting-azure-portal.md)
- [Portail classique Azure](active-directory-reporting-guide.md)

*Cette documentation fait partie du [Guide Azure Active Directory création de rapports](active-directory-reporting-guide.md).*

Les rapports dans l’aperçu Azure Active Directory, vous obtenez toutes les informations que nécessaires pour déterminer le faire de votre environnement. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md)

Il existe deux zones principales de création de rapports :

- **Connexion à des activités** – informations sur l’utilisation des applications et des activités de connexion à l’utilisateur

- **Journaux d’audit** - informations sur l’activité système sur les utilisateurs et gestion de groupe, vos applications gérées et opérations d’annuaire

Selon l’étendue des données que vous recherchez, vous pouvez consulter ces rapports en cliquant sur les **utilisateurs et groupes** ou **des applications d’entreprise** dans la liste des services dans le [portail Azure](https://portal.azure.com).

## <a name="sign-in-activities"></a>Activités de connexion

### <a name="user-sign-in-activities"></a>Activités de connexion à l’utilisateur

Avec les informations fournies par l’état de connexion utilisateur, vous trouvez des réponses aux questions telles que :

- Quel est le modèle de connexion d’un utilisateur ?
- Combien d’utilisateurs ont utilisateurs connectés via une semaine ?
- Quel est l’état de ces compléments se ?

Votre point d’entrée pour ces données est le graphique de connexion utilisateur dans la section **vue d’ensemble** , sous **utilisateurs et groupes**.

 ![Création de rapports] (./media/active-directory-reporting-azure-portal/05.png "Création de rapports")

Le graphique de connexion utilisateur affiche les agrégations hebdomadaires du signe ins pour tous les utilisateurs dans une période donnée. La valeur par défaut pour la période est de 30 jours.

![Création de rapports] (./media/active-directory-reporting-azure-portal/02.png "Création de rapports")

Lorsque vous cliquez sur un jour dans le graphique se connecter, vous obtenez une liste détaillée des activités de se connecter.

![Création de rapports] (./media/active-directory-reporting-azure-portal/03.png "Création de rapports")

Chaque ligne dans la liste des activités de connexion vous donne des informations détaillées sur la connexion sélectionnée telles que :

- Qui a signé ?

- Quel est le nom UPN connexe ?

- Quelle application a été la cible de la connexion ?

- Quelle est l’adresse IP de la connexion ?

- Quel est l’état de la connexion ?

### <a name="usage-of-managed-applications"></a>Utilisation des applications gérées

Avec une vue centré sur les applications de vos données de connexion, vous pouvez répondez aux questions telles que :

- Qui est à l’aide de mes applications ?

- Quelles sont les principales 3 applications dans votre organisation ?

- J’ai récemment transférées une application. Comment fait ?


Votre point d’entrée pour ces données est les 3 principales applications de votre organisation dans le dernier rapport de 30 jours dans la section **vue d’ensemble** sous **applications d’entreprise**.

 ![Création de rapports] (./media/active-directory-reporting-azure-portal/06.png "Création de rapports")


Les agrégations hebdomadaire graph application l’utilisation de vous connecter ins pour vos applications haut 3 dans une période donnée. La valeur par défaut pour la période est de 30 jours.

![Création de rapports] (./media/active-directory-reporting-azure-portal/78.png "Création de rapports")

Si vous le souhaitez, vous pouvez définir le focus sur une application spécifique.

![Création de rapports] (./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Création de rapports")


Lorsque vous cliquez sur un jour dans le graphique de l’utilisation de l’application, vous obtenez une liste détaillée des activités de se connecter.


![Création de rapports] (./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Création de rapports")



L’option **se-ins** vous donne une vue d’ensemble complet de tous les événements de connexion à vos applications.

![Création de rapports] (./media/active-directory-reporting-azure-portal/85.png "Création de rapports")

À l’aide du sélecteur de colonnes, vous pouvez sélectionner les champs de données que vous voulez afficher.

![Création de rapports] (./media/active-directory-reporting-azure-portal/column_chooser.png "Création de rapports")



### <a name="filtering-sign-ins"></a>Filtrage des connexions

Vous pouvez filtrer des compléments d’authentification par un intervalle de temps pour limiter la quantité de données affichées.

![Création de rapports] (./media/active-directory-reporting-azure-portal/927.png "Création de rapports")


Une autre méthode pour filtrer les entrées des activités connexion consiste à rechercher des entrées spécifiques.
La méthode de recherche vous permet de définir la portée votre signe-ins autour des **utilisateurs**spécifiques, des **groupes** ou des **applications**.


![Création de rapports] (./media/active-directory-reporting-azure-portal/84.png "Création de rapports")

## <a name="audit-logs"></a>Journaux d’audit

Les journaux d’audit dans Azure Active Directory fournissent des enregistrements d’activités système fins de conformité.

Il existe trois catégories principales pour l’audit des activités associées dans le portail Azure :

- Utilisateurs et groupes   

- Applications

- Répertoire   


Pour une liste complète des activités de rapport d’audit, consultez la [liste des événements de rapport d’audit](active-directory-reporting-audit-events.md#list-of-audit-report-events).


Votre point d’entrée à toutes les données d’audit est **journaux d’Audit** dans la section **activité** de **Azure Active Directory**.


![L’audit] (./media/active-directory-reporting-azure-portal/61.png "L’audit")


Un journal d’audit comporte un affichage de liste qui présente les intervenants (qui), les activités (quoi) et les cibles.


![L’audit] (./media/active-directory-reporting-azure-portal/345.png "L’audit")


En cliquant sur un élément dans la liste, vous pouvez obtenir plus de détails.

![L’audit] (./media/active-directory-reporting-azure-portal/873.png "L’audit")




### <a name="users-and-groups-audit-logs"></a>Utilisateurs et groupes des journaux d’audit


Avec un utilisateur et les rapports d’audit au niveau de groupe, vous pouvez obtenir des réponses aux questions telles que :

- Les types de mises à jour ont été appliqués les utilisateurs ?

- Combien d’utilisateurs ont été modifiés ?

- Nombre de mots de passe ont été modifié ?

- Ce qui a fait un administrateur dans un répertoire ?

- Quels sont les groupes qui ont été ajoutés ?

- Existe-t-il des groupes avec les modifications d’appartenance ?

- Les propriétaires du groupe ont été modifiés ?

- Quelles licences ont été affectées à un groupe ou un utilisateur ?


Si vous voulez simplement consulter les données d’audit qui sont liées à des utilisateurs et groupes, vous pouvez trouver un affichage filtré sous **journaux d’Audit** dans la section de **l’activité** des **utilisateurs et groupes**.


![L’audit] (./media/active-directory-reporting-azure-portal/93.png "L’audit")


### <a name="application-audit-logs"></a>Journaux d’audit d’application

Rapports d’audit avec basée sur l’application, vous pouvez obtenir des réponses aux questions telles que :

- Quelles sont les applications qui ont été ajoutées ou mis à jour ?

- Quelles sont les applications qui ont été supprimées ?

- Principal du service pour une application qui a changé ?

- Les noms des applications ont été modifiés ?

- Qui a communiqué consentement à l’application ?


Si vous voulez simplement consulter les données d’audit liées aux applications, vous pouvez trouver un affichage filtré sous **journaux d’Audit** dans la section **activité** des **applications**d’entreprise.


![L’audit] (./media/active-directory-reporting-azure-portal/134.png "L’audit")


### <a name="filtering-audit-logs"></a>Journaux d’audit de filtrage

Vous pouvez filtrer un rapport d’audit par un intervalle de temps pour limiter la quantité de données affichées.

![L’audit] (./media/active-directory-reporting-azure-portal/324.png "L’audit")

Une autre méthode pour filtrer les entrées du journal d’audit est pour rechercher des entrées spécifiques.

![L’audit] (./media/active-directory-reporting-azure-portal/237.png "L’audit")

## <a name="next-steps"></a>Étapes suivantes

Consultez [Azure Active Directory Guide des rapports](active-directory-reporting-guide.md).
