<properties
    pageTitle="Azure AD Connect : Étapes suivantes et explique comment gérer Azure AD Connect | Microsoft Azure"
    description="Découvrez comment étendre la configuration par défaut et les tâches opérationnelles pour Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Étapes suivantes et comment gérer Azure AD Connect
Les éléments suivants sont avancées opérationnelles rubriques qui vous permettent de personnaliser Azure Active Directory se connecter pour répondre aux exigences et les besoins de votre organisation.  

## <a name="add-additional-sync-administrators"></a>Ajouter des administrateurs de synchronisation supplémentaires
Par défaut, seul l’utilisateur qui a fait l’installation et les administrateurs local sera chargée de gérer le moteur de synchronisation installés. Pour les autres personnes puissent accéder et gérer le moteur de synchronisation, repérez le groupe nommé ADSyncAdmins sur le serveur local et ajoutez-les à ce groupe.

## <a name="assigning-licenses-to-azure-ad-premium-and-enterprise-mobility-users"></a>Attribution de licences pour les utilisateurs Azure AD Premium et mobilité d’entreprise

À présent que vos utilisateurs ont été synchronisées dans le cloud, vous devrez leur attribuer une licence afin qu’ils peuvent commencer avec les applications cloud tels que Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Pour affecter un Azure AD Premium ou la licence de la Suite entreprise mobilité
--------------------------------------------------------------------------------
1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sur la gauche, sélectionnez **Active Directory**.
3. Dans la page Active Directory, double-cliquez sur le répertoire qui compte les utilisateurs que vous souhaitez activer.
4. En haut de la page annuaire, sélectionnez **licences**.
5. Dans la page licences, sélectionnez Active Directory Premium ou entreprise mobilité Suite, puis cliquez sur **attribuer**.
6. Dans la boîte de dialogue, sélectionnez les utilisateurs que vous voulez attribuer des licences à, puis cliquez sur l’icône de coche pour enregistrer les modifications.


## <a name="verifying-the-scheduled-synchronization-task"></a>Vérification de la tâche de synchronisation planifiée
Si vous souhaitez vérifier l’état d’une synchronisation que vous pouvez le faire en vérifiant dans le portail Azure.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Pour vérifier la tâche de synchronisation planifiée
--------------------------------------------------------------------------------
1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sur la gauche, sélectionnez **Active Directory**.
3. Dans la page Active Directory, double-cliquez sur le répertoire qui compte les utilisateurs que vous souhaitez activer.
4. En haut de la page annuaire, sélectionnez **Intégration d’annuaire**.
5. Sous l’intégration avec la note active directory local la dernière synchronisation.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="starting-a-scheduled-synchronization-task"></a>Démarrage d’une tâche de synchronisation planifiée
Si vous avez besoin exécuter une tâche de synchronisation procéder en relançant l’Assistant Azure AD Connect.  Vous devrez fournir vos informations d’identification Azure AD.  Dans l’Assistant, sélectionnez la tâche de **Personnaliser les options de synchronisation** , puis cliquez sur suivant dans l’Assistant. À la fin, assurez-vous que la case **Démarrer le processus de synchronisation dès la fin de la configuration initiale** est cochée.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Pour plus d’informations sur la synchronisation Azure AD Connect : Planificateur, voir [Se connecter le Planificateur de Azure AD](active-directory-aadconnectsync-feature-scheduler.md)


## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tâches supplémentaires disponibles dans Azure AD Connect
Après l’installation initiale de Azure AD Connect, vous pouvez toujours démarrer l’Assistant nouveau à partir de la page ou bureau un raccourci de démarrer Azure AD Connect.  Vous remarquerez que passer par l’Assistant Nouveau offre de nouvelles options sous forme de tâches supplémentaires.  

Le tableau suivant fournit un résumé de ces tâches et une brève description sur chacun d’eux.

![Participer à une règle](./media/active-directory-aadconnect-whats-next/addtasks.png)


Tâche supplémentaire | Description
------------- | ------------- |
Afficher le scénario sélectionné  |Vous permet d’afficher votre solution Azure AD Connect actuelle.  Cela inclut les paramètres généraux, annuaires synchronisés, les paramètres de synchronisation, etc..
Personnaliser les options de synchronisation | Vous pouvez modifier la configuration en cours, notamment l’ajout de forêt Active Directory supplémentaire à la configuration ou l’activation des options de synchronisation comme utilisateur, groupe, appareil ou mot de passe en écriture différée.
Activer le Mode de mise en attente |  Cela vous permet des informations d’étape qui seront plus tard synchronisées, mais rien n’est exportée vers Azure Active Directory ou Active Directory.  Cela vous permet d’afficher un aperçu des synchronisations avant qu’ils se produisent.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
