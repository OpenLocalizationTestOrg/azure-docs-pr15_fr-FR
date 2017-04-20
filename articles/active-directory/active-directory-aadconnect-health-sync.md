
<properties
    pageTitle="L’utilisation d’état connecter Azure AD avec la synchronisation | Microsoft Azure"
    description="Il s’agit de la page Azure AD connecter santé qui sera consacrée à surveiller synchronisation Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-for-sync"></a>L’utilisation d’état connecter Azure AD pour la synchronisation
La documentation suivante est spécifique à l’analyse d’Azure AD Connect (synchronisation) dont l’état de connexion AD Azure.  Pour plus d’informations sur la surveillance AD FS dont l’état de connexion AD Azure consultez [à l’aide de Azure AD connecter santé avec AD FS](active-directory-aadconnect-health-adfs.md). En outre, pour plus d’informations sur le contrôle des Services de domaine Active Directory dont l’état de connexion AD Azure consultez [à l’aide de Azure AD connecter santé avec les services AD DS](active-directory-aadconnect-health-adds.md).

![Azure AD Connect santé pour la synchronisation](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Alertes d’Azure intégrité AD se connecter pour la synchronisation
La Azure AD se connecter au niveau des alertes pour la section synchronisation fournit la liste des alertes actives. Chaque alerte inclut des informations pertinentes, procédure de résolution et des liens vers la documentation associée. En sélectionnant une alerte active ou résolue, vous verrez une nouvelle carte avec des informations supplémentaires, ainsi que les étapes à suivre pour résoudre l’alerte et des liens vers la documentation supplémentaire. Vous pouvez également afficher les données historiques sur les alertes qui ont été résolus par le passé.

En sélectionnant une alerte que vous seront fournies avec des informations supplémentaires, ainsi que les étapes, vous pouvez prendre pour résoudre l’alerte et des liens vers la documentation supplémentaire.

![Erreur de synchronisation Azure AD Connect](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Évaluation limitée d’alertes
Si Azure AD Connect n’utilise pas la configuration par défaut (par exemple, si l’attribut filtrage est modifié à partir de la configuration par défaut pour une configuration personnalisée), puis l’agent d’Azure AD connecter intégrité pas télécharger les événements d’erreur relatifs à Azure AD Connect.

Cela limite l’évaluation des alertes par le service. Vous s’afficher une bannière qui indique cette condition dans le portail Azure sous votre service.

![Azure AD Connect santé pour la synchronisation](./media/active-directory-aadconnect-health-sync/banner.png)

Vous pouvez le modifier en cliquant sur « Paramètres », qui permet à agent Azure AD connecter santé télécharger tous les journaux d’erreurs.

![Azure AD Connect santé pour la synchronisation](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>Aperçu de synchronisation
Les administrateurs fréquemment souhaitez connaître la durée nécessaire pour synchroniser les modifications à Azure Active Directory et de la quantité de modifications qui a lieu. Cette fonctionnalité est un moyen simple pour cela visualiser à l’aide de l’en dessous de graphiques :   

- Latence des opérations de synchronisation
- Tendance de modification d’objet

### <a name="sync-latency"></a>Latence de synchronisation
Cette fonctionnalité fournit une tendance de la latence des opérations de synchronisation (importation, exportation, etc.) pour les connecteurs des graphique.  Il fournit un moyen rapide et facile à comprendre non seulement la latence de vos opérations (davantage si vous disposez d’un ensemble volumineux de modifications simultanées), mais aussi un moyen de détecter les anomalies dans le temps de latence qui peut nécessiter un examen supplémentaire.

![Latence de synchronisation](./media/active-directory-aadconnect-health-sync/synclatency02.png)

Par défaut, uniquement la latence de l’opération « Export » pour le connecteur Azure AD est affichée.  Pour afficher davantage d’opérations sur le connecteur ou pour afficher les opérations à partir d’autres connecteurs, un clic droit sur le graphique, sélectionnez Modifier le graphique ou cliquez sur le bouton « Modifier le graphique latence » et sélectionnez l’opération spécifique et les connecteurs.

### <a name="sync-object-changes"></a>Synchroniser les modifications objet
Cette fonctionnalité fournit une tendance graphique du nombre de modifications qui sont évaluées et exportés vers Azure AD.  Aujourd'hui, essayez de collecter les informations sur les journaux de synchronisation est difficile.  Le graphique vous donne, non seulement un moyen très simple d’analyser le nombre de modifications qui se produisent dans votre environnement, mais aussi visualiser les erreurs qui se produisent.

![Latence de synchronisation](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>Rapport d’erreurs de synchronisation de niveau objet (Preview)
Cette fonctionnalité fournit un rapport sur les erreurs de synchronisation qui peuvent se produire lorsque les données d’identité sont synchronisées entre Windows Server AD et Azure AD à l’aide de Azure AD Connect.

- Le rapport couvre erreurs enregistrées par le client de synchronisation (Azure AD Connect version 1.1.281.0 ou une version ultérieure)
- Il inclut les erreurs qui se sont produites dans la dernière opération de synchronisation sur le moteur de synchronisation. (« Exporter » sur le lien Azure AD.)
- Azure agent d’intégrité de se connecter AD pour la synchronisation doit être connectés sortante aux points de terminaison requis pour le rapport inclure les données les plus récentes. Consultez la [section Configuration requise](active-directory-aadconnect-health-agent-install.md#Requirements) pour plus d’informations.
- Le rapport est **mis à jour après 30 minutes** en utilisant les données téléchargées par l’agent de la santé Azure AD se connecter pour la synchronisation.
Il fournit les fonctionnalités clées suivantes

    - Catégorisation d’erreurs
    - Liste des objets avec l’erreur par catégorie
    - Toutes les données sur les erreurs en un seul endroit
    - Côté par comparaison des objets avec l’erreur en raison d’un conflit
    - Télécharger le rapport d’erreurs sous forme d’un CV (bientôt disponible)

### <a name="categorization-of-errors"></a>Catégorisation d’erreurs
Le rapport classe les erreurs de synchronisation existant dans les catégories suivantes :

| Catégorie | Description |
| -------------- | ----------- |
| Attribut en double | Erreurs lors de tentatives d’Azure AD Connect créer ou mettre à jour des objets avec doublons d’un ou plusieurs attributs dans Azure AD qui doit être unique dans un client, par exemple proxyAddresses, UserPrincipalName. |
| Incompatibilité de données | Erreurs lors de la correspondance bordures ne parvient pas à la correspondance des objets qui génèrent des erreurs de synchronisation. |
| Échec de Validation des données | Erreurs en raison de données non valides, tels que des caractères non pris en charge dans les attributs critiques tels que UserPrincipalName, mettre en forme les erreurs de validation avant d’être écrites dans Azure Active Directory a échoué.|
| Attribut de grande taille | Erreurs lors de l’un ou plusieurs attributs sont supérieures à la taille autorisée, leur longueur ou count.|
| Autres | Toutes les autres erreurs qui ne tient pas dans les catégories ci-dessus. En fonction des commentaires, cette catégorie sera fractionnée dans sous-catégories.

![Résumé du rapport d’erreur de synchronisation](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![synchroniser des catégories de rapport d’erreur](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>Liste des objets avec l’erreur par catégorie
Extraire dans chaque catégorie doit fournir la liste des objets ayant l’erreur dans cette catégorie.
![Liste de rapports d’erreur de synchronisation](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Détails de l’erreur
Suivre des données est disponible dans la vue détaillée pour chaque erreur

- Identificateurs de l' *Objet AD* impliquées
- Identificateurs de l' *Objet AD Azure* impliqués (le cas échéant)
- Description de l’erreur et comment résoudre le problème
- Articles connexes

![Détails de rapport d’erreur de synchronisation](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>Télécharger le rapport d’erreurs au format CSV
Cette fonctionnalité sera bientôt disponible. Restez en ligne pour des mises à jour.



## <a name="related-links"></a>Liens connexes
* [Résolution des erreurs lors de la synchronisation](active-directory-aadconnect-troubleshoot-sync-errors.md)
* [Attribut dupliqué résilience](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect d’intégrité](active-directory-aadconnect-health.md)
* [Azure AD Connect Installation de l’Agent d’intégrité](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect opérations d’intégrité](active-directory-aadconnect-health-operations.md)
* [À l’aide de Azure AD connectez santé avec AD FS](active-directory-aadconnect-health-adfs.md)
* [À l’aide de Azure AD connectez santé avec les services AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect santé Forum aux questions](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect historique des versions d’intégrité](active-directory-aadconnect-health-version-history.md)
