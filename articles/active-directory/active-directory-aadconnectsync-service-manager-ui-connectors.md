<properties
    pageTitle="Azure AD Connect synchronisation : Gestionnaire de services de synchronisation UI | Microsoft Azure"
    description="Comprendre l’onglet connecteurs dans le Gestionnaire de services de synchronisation pour Azure AD Connect."
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
    ms.date="09/07/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Azure AD Connect synchronisation : Gestionnaire de services de synchronisation

[Opérations](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Connecteurs](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Concepteur de méta-verse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Recherche](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

L’onglet connecteurs permet de gérer tous les systèmes à que le moteur de synchronisation est connecté.

## <a name="connector-actions"></a>Actions de connecteur

Action | Commentaire
--- | ---
Créer | N’utilisez pas. Pour vous connecter à forêt AD supplémentaire, utilisez l’Assistant installation.
Propriétés | Utilisé pour le domaine et le filtrage de l’unité d’organisation.
[Supprimer](#delete) | Utilisé pour supprimer les données dans l’espace de connecteur ou pour supprimer la connexion à une forêt.
[Configurer les profils d’exécution](#configure-run-profiles) | À l’exception de domaine filtrage, rien à configurer ici. Vous pouvez utiliser cette action pour afficher les profils d’exécution déjà configurés.
Exécuter | Permet de lancer un unique d’un profil.
Arrêter | Arrête un connecteur un profil en cours d’exécution.
Connecteur d’exportation | N’utilisez pas.
Connecteur importation | N’utilisez pas.
Lien de mise à jour | N’utilisez pas.
Actualiser le schéma | Actualise le schéma mis en cache. Il est préférable d’utiliser l’option dans l’Assistant installation à la place, étant donné que ce également mises à jour synchroniser des règles.
[Espace de connecteur de recherche](#search-connector-space) | Permet de rechercher des objets et de [suivre un objet et ses données au sein du système](#follow-an-object-and-its-data-through-the-system).

### <a name="delete"></a>Supprimer
L’action supprimer est utilisée pour deux choses différentes.
![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

L’option **Supprimer uniquement l’espace de connecteur** supprime toutes les données, tout en conservant la configuration.

L’option **Supprimer le lien et espace de connecteur** supprime les données et la configuration. Cette option est utilisée lorsque vous ne souhaitez pas vous connecter à une forêt plus.

Les deux options synchroniser tous les objets et mettre à jour les objets métaverse. Cette action est une opération longue.

### <a name="configure-run-profiles"></a>Configurer les profils d’exécution
Cette option permet de voir les profils d’exécution configurés pour un connecteur.

![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Espace de connecteur de recherche
L’action Rechercher connecteur espace est utile pour rechercher des objets et résoudre les problèmes de données.

![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Commencez par sélectionner une **étendue**. Vous pouvez rechercher des données en fonction de (nom unique relatif, nom de domaine, point d’ancrage, arborescence secondaire), ou l’état de l’objet (toutes les autres options).  
![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Si vous effectuez par exemple une recherche arborescence secondaire, vous obtenez tous les objets dans une unité d’organisation.
![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png) dans cette grille, vous pouvez sélectionner un objet, sélectionnez **Propriétés**, puis [Utilisez-le](#follow-an-object-and-its-data-through-the-system) à partir de l’espace de connecteur source, le métaverse et à l’espace de connecteur cible.

## <a name="follow-an-object-and-its-data-through-the-system"></a>Suivre un objet et ses données au sein du système
Lorsque vous résolvez un problème avec les données, suivre un objet à partir de l’espace de connecteur source, dans le métaverse et à la cible espace de connecteur est une procédure clée à comprendre pourquoi données n’ayant pas les valeurs prévues.

### <a name="connector-space-object-properties"></a>Propriétés de l’objet espace connecteur
**Importation**  
Lorsque vous ouvrez un objet SC, il existe plusieurs onglets dans la partie supérieure. L’onglet **Importer** affiche les données transférées après une importation.
![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png) l' **Ancienne valeur** affiche ce qui est stocké dans le système et la **Nouvelle valeur** ce qui a été reçu à partir du système source et n’a pas encore été appliqué. Dans ce cas, car il existe une erreur de synchronisation, la modification ne peut pas être appliquée.

**Erreur**  
La page d’erreur n’est visible s’il existe un problème avec l’objet. Afficher les détails dans la page opérations pour plus d’informations sur la façon de [résoudre les erreurs de synchronisation](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab).

**Enregistrement en ligne**  
L’onglet lignage indique comment l’objet espace connecteur est liée à l’objet du métaverse. Vous pouvez voir le connecteur dernière importation une modification du système connecté et les règles appliquées à compléter les données dans le méta-verse.
![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png) dans la colonne **Action** , vous pouvez voir une règle de synchronisation **entrant** avec l’action **mise en service**. Cela signifie que tant que cet objet espace connecteur est présent, l’objet du métaverse reste. Si la liste des règles de synchronisation à la place affiche une règle de synchronisation avec la direction **sortant** et la **mise en service**, elle indique que cet objet est supprimé lorsque l’objet du métaverse est supprimé.
![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png) vous pouvez également voir dans la colonne **PasswordSync** que l’espace de connecteur entrant peut y apporter des modifications au mot de passe dans la mesure où une seule règle de synchronisation a la valeur **True**. Ce mot de passe est envoyé puis Azure AD via la règle sortante.

Sous l’onglet enregistrement en ligne, vous pouvez accéder au métaverse en cliquant sur [Propriétés de l’objet du métaverse](#metaverse-object-properties).

En bas de tous les onglets sont deux boutons : **Aperçu** et **Connectez-vous**.

**Aperçu**  
La page d’aperçu est utilisée pour synchroniser un objet unique. Il est utile si vous résolvez certaines règles de synchronisation client et que vous souhaitez voir l’effet d’un changement d’un seul objet. Vous pouvez choisir entre **Synchronisation complète** et **synchronisation Delta**. Vous pouvez également sélectionner **Générer un aperçu**, qui le conserve uniquement la modification en mémoire, **Valider Preview**, quels stades tous les remplace par un espace de connecteur cible.
![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png) vous pouvez inspecter l’objet et appliquées à la règle d’un flux attribut particulier.
![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**Journal**  
La page de journal est utilisée pour afficher l’état de synchronisation de mot de passe et l’historique, voir [synchronisation de mot de passe de résolution des problèmes](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization) pour plus d’informations.

### <a name="metaverse-object-properties"></a>Propriétés de l’objet du métaverse
**Attributs**  
Sous l’onglet attributs, vous pouvez afficher les valeurs et quel connecteur contribué il.
![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png)
**connecteurs**  
L’onglet connecteurs affiche tous les espaces de connecteur qui ont une représentation de l’objet.
![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png) cet onglet vous permet également de permettant d’accéder à l' [objet espace de connecteur](#connector-space-object-properties).

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration [Azure AD Connect synchroniser](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
