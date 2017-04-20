<properties
    pageTitle="Azure AD Connect synchronisation : Gestionnaire de services de synchronisation UI | Microsoft Azure"
    description="Comprendre l’onglet opérations dans le Gestionnaire de services de synchronisation pour Azure AD Connect."
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

![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

L’onglet opérations affiche les résultats des plus récentes opérations. Cet onglet est crucial pour comprendre et résoudre les problèmes.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Comprendre les informations visibles dans l’onglet opérations
La partie supérieure gauche affiche toutes les séries de chroniques. Par défaut, le journal des opérations conserve les informations relatives au cours des 7 derniers jours, mais ce paramètre peut être modifié avec le [Planificateur](active-directory-aadconnectsync-feature-scheduler.md). Vous souhaitez rechercher pour toutes les exécutions qui n’affiche pas un message de réussite. Vous pouvez modifier le tri en cliquant sur les en-têtes.

La colonne **statut** est informations les plus importantes et affiche le problème plus grave pour une exécution. Voici un résumé des statuts courants dans l’ordre de priorité pour déterminer l’origine (où * indiquer plusieurs chaînes d’erreur possibles).

État | Commentaire
--- | ---
arrêté-* | L’exécution n’a pas pu se terminer. Par exemple, si le système distant est vers le bas et ne peut pas être contacté.
limite d’erreur arrêté | Il existe plus de 5 000 erreurs. L’exécution a été arrêtée automatiquement car un grand nombre d’erreurs.
terminé -\*-erreurs | L’exécution terminée, mais il existe des erreurs (moins de 5 000) qui doivent être analysés.
terminé -\*-avertissements | L’exécution terminée, mais certaines données n’est pas dans l’état attendu. Si vous avez des erreurs, ce message est généralement uniquement une manifestation. Jusqu'à ce que vous avez résolu les erreurs, vous devez examiner pas avertissements.
opération réussie | Aucun problème.

Lorsque vous sélectionnez une ligne, bas met à jour pour afficher les détails de qui s’exécutent. À l’extrémité gauche de la partie inférieure, vous pouvez avoir une liste indiquant **étape #**. Cette liste s’affiche uniquement si vous avez plusieurs domaines dans votre forêt où chaque domaine est représenté par une étape. Le nom de domaine sont accessibles sous l’en-tête **Partition**. Sous **Statistiques de synchronisation**, vous pouvez trouver plus d’informations sur le nombre de modifications qui ont été traitées. Vous pouvez cliquer sur les liens pour obtenir une liste des objets modifiés. Si vous avez des objets avec des erreurs, ces erreurs apparaissent sous les **Erreurs de synchronisation**.

## <a name="troubleshoot-errors-in-operations-tab"></a>Résoudre les erreurs dans l’onglet opérations
![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
Lorsque vous avez des erreurs, à la fois l’objet erreur et l’erreur elle-même sont des liens qui donne des informations supplémentaires.

Commencez par cliquer sur la chaîne d’erreur (**synchronisation-règle-erreur-fonction-déclenchée** dans l’image). Tout d’abord s’affiche une vue d’ensemble de l’objet. Pour afficher l’erreur, cliquez sur le bouton **Suivi de la pile**. Cette trace fournit les informations de débogage au niveau de l’erreur.

**Conseil :** Vous pouvez avec le bouton droit dans la zone **informations sur la pile d’appel** , cliquez sur **Sélectionner tout**, puis sur **Copier**. Vous pouvez copier la pile et examinez l’erreur dans votre éditeur préféré, tel que le bloc-notes.

- Si l’erreur est de **SyncRulesEngine**, informations de la pile a tout d’abord une liste de tous les attributs sur l’objet. Faites défiler vers le bas jusqu'à ce que vous voyiez le titre **InnerException = >**.  
![Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
La ligne après indique l’erreur. Dans l’image ci-dessus, l’erreur provient d’une Fabrikam de règle synchronisation personnalisé créé.

Si l’erreur elle-même ne fournit pas assez d’informations, il est temps d’examiner les données lui-même. Vous pouvez cliquer sur le lien avec l’identificateur d’objet et [Suivez un objet et ses données au sein du système](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration [Azure AD Connect synchroniser](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
