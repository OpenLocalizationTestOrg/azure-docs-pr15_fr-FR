
<properties
    pageTitle="À l’aide de Azure AD connectez santé avec les services AD DS | Microsoft Azure"
    description="Il s’agit de la page Azure AD connecter santé qui sera consacrée à surveiller les services AD DS."
    services="active-directory"
    documentationCenter=""
    authors="arluca"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="arluca"/>

# <a name="using-azure-ad-connect-health-with-ad-ds"></a>À l’aide de Azure AD connectez santé avec les services AD DS
La documentation suivante est spécifique à l’analyse d’Active Directory Domain Services dont l’état de connexion AD Azure. Les versions prises en charge des services AD DS : Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2.

Pour plus d’informations sur la surveillance AD FS dont l’état de connexion AD Azure, reportez-vous [à l’aide de Azure AD connecter santé avec AD FS](active-directory-aadconnect-health-adfs.md). En outre, pour plus d’informations sur la surveillance Azure AD Connect (synchronisation) dont l’état de connexion AD Azure voir [AD de Azure à l’aide de se connecter au niveau pour la synchronisation](active-directory-aadconnect-health-sync.md).

![Azure AD Connect santé pour AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Des alertes pour Azure AD connectent au niveau AD DS
La section alertes dans Azure AD connecter santé pour AD DS, vous fournit la liste des alertes actifs et résolus, liées à votre domaine. Sélection d’une alerte active ou résolue ouvre une nouvelle carte avec des informations supplémentaires, ainsi que de la procédure de résolution et liens vers des documents à l’appui. Chaque type d’alerte peut avoir une ou plusieurs instances correspondant à chaque contrôleur de domaine concerné par cette alerte particulière. Au bas de la cuillère alerte, vous pouvez double-cliquer sur un contrôleur de domaine affecté pour ouvrir une carte supplémentaire avec plus d’informations sur cette instance d’alerte.

Dans cette carte, vous pouvez activer les notifications par courrier électronique pour les alertes et modifier la période dans l’affichage. Développer la plage horaire permet de voir préalables alertes résolues.

![Erreur de synchronisation Azure AD Connect](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>Tableau de bord domaine contrôleurs
Ce tableau de bord fournit une vue topologie de votre environnement, ainsi que les mesures opérationnelles clés et l’état de chaque contrôleur de domaine analysé. Les mesures présentées aider à identifier rapidement les contrôleurs peuvent nécessiter un examen supplémentaire. Par défaut, uniquement un sous-ensemble des colonnes s’affiche. Cependant, vous pouvez trouver l’ensemble des colonnes disponibles, en double-cliquant sur la commande de colonnes. Sélectionner les colonnes plus sujet d’intérêt, désactive ce tableau de bord simple et en une seule placer pour afficher l’état de votre environnement AD DS.

![Domaine](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

Domaine peut être regroupés par leur domaine respectif ou de site, ce qui est utile pour comprendre la topologie de l’environnement. Enfin, si vous double-cliquez sur l’en-tête de carte, le tableau de bord agrandit pour utiliser l’immobilier disponible à l’écran. Cette vue plus grande est utile lorsque plusieurs colonnes sont affichées.

## <a name="replication-status-dashboard"></a>Tableau de bord réplication état
Ce tableau de bord fournit un affichage de l’état de la réplication et la topologie de réplication contrôleur de domaine analysé. L’état de la dernière tentative de réplication est répertorié, ainsi que de documentation utile pour les erreurs qui sont trouve. Vous pouvez double-cliquer sur un contrôleur de domaine avec un message d’erreur, pour ouvrir une nouvelle carte comportant des informations telles que : détails sur l’erreur, recommandé procédure de résolution et des liens vers la documentation de dépannage.

![État de la réplication](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>Surveillance des mots clés
Cette fonctionnalité fournit les tendances graphiques différents des compteurs de performances, qui sont collectées de manière continue à partir de chaque contrôleur de domaine géré. Performances d’un contrôleur de domaine peuvent être facilement comparées entre contrôlée Active Directory de votre forêt. En outre, vous pouvez voir différents compteurs côte à côte, qui est utile lors de la résolution des problèmes dans votre environnement.

![Surveillance des mots clés](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

Par défaut, nous avons présélectionnés quatre compteurs de performance ; Toutefois, vous pouvez inclure d’autres personnes en cliquant sur la commande filtrer et sélectionner ou désélectionner tout compteur de performance souhaité. En outre, vous pouvez double-cliquer sur un graphique de compteur de performance pour ouvrir une nouvelle carte, qui inclut les points de données pour chaque contrôleur de domaine géré.

## <a name="related-links"></a>Liens connexes

* [Azure AD Connect d’intégrité](active-directory-aadconnect-health.md)
* [Azure AD Connect Installation de l’Agent d’intégrité](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect opérations d’intégrité](active-directory-aadconnect-health-operations.md)
* [À l’aide de Azure AD connectez santé avec AD FS](active-directory-aadconnect-health-adfs.md)
* [L’utilisation d’état connecter Azure AD pour la synchronisation](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect santé Forum aux questions](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect historique des versions d’intégrité](active-directory-aadconnect-health-version-history.md)
