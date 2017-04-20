<properties
   pageTitle="Azure AD Connect synchronisation : pas de suppression accidentelle | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité empêcher suppressions accidentelles (empêcher la suppression accidentelle) Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Azure AD Connect synchronisation : pas de suppression accidentelle
Cette rubrique décrit la fonctionnalité empêcher suppressions accidentelles (empêcher la suppression accidentelle) Azure AD Connect.

Lorsque l’installation d’Azure AD Connect, empêchent accidentelle suppressions est activée par défaut et configuré pour ne pas autoriser une exportation avec plus de 500 suppressions. Cette fonctionnalité est conçue pour vous protéger contre les modifications accidentelles et les modifications apportées à votre annuaire local qui affecte de nombreux utilisateurs et d’autres objets.

Scénarios courants lorsque vous voyez suppressions nombreuses inclure :

- Modifications apportées au [filtrage](active-directory-aadconnectsync-configure-filtering.md) où une entière [unité d’organisation](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) ou un [domaine](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) est désactivée.
- Tous les objets dans une unité d’organisation sont supprimés.
- Une unité d’organisation est renommée afin que tous les objets qu’il contient sont considérés comme se déconnecter de l’étendue pour la synchronisation.

La valeur par défaut de 500 objets peut être modifiée avec PowerShell à l’aide de `Enable-ADSyncExportDeletionThreshold`. Vous devez configurer cette valeur pour l’ajuster à la taille de votre organisation. Étant donné que le Planificateur de synchronisation s’exécute toutes les 30 minutes, la valeur est le nombre de suppressions vu dans les 30 minutes.

S’il y a trop de suppressions intermédiaire pour être exportés vers Azure AD, puis l’exportation s’arrête et vous recevez un message électronique comme suit :

![Empêcher les suppressions accidentels messagerie](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Bonjour (contact technique). À (heure), le service de synchronisation d’identité détecté que le nombre de suppressions a dépassé le seuil de suppression configuré pour (nom de l’organisation). Un nombre total de (nombre) d’objets a été envoyé pour la suppression dans cette synchronisation identité exécuter. Cela satisfait ou a dépassé la valeur de seuil configuré suppression d’objets (nombre). Nous avons besoin de vous permettent de fournir une confirmation que ces suppressions doivent être traitées avant que nous continuera. Voir les suppressions accidentelles prévention pour plus d’informations sur l’erreur indiquée dans ce message électronique.*

Vous pouvez également consulter l’état `stopped-deletion-threshold-exceeded` s’affichent dans le **Gestionnaire de services de synchronisation** de l’interface utilisateur pour le profil d’exportation.
![Pas de suppression accidentelle UI Gestionnaire de services de synchronisation](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

S’il s’agissait inattendu, puis identifier et prendre des mesures verres. Pour voir les objets sur le point d’être supprimé, procédez comme suit :

1. Démarrer le **Service de synchronisation** dans le Menu Démarrer.
2. Accédez aux **connecteurs**.
3. Sélectionnez le connecteur à type **Azure Active Directory**.
4. Sous **Actions** vers la droite, sélectionnez **l’Espace de connecteur de recherche**.
5. Dans la fenêtre contextuelle sous **étendue**, sélectionnez **Déconnecté depuis** et choisissez une heure dans le passé. Cliquez sur **Rechercher**. Cette page fournit un affichage de tous les objets sur le point d’être supprimé. En cliquant sur chaque élément, vous pouvez obtenir plus d’informations sur l’objet. Vous pouvez également cliquer sur la **Colonne paramètre** pour ajouter des attributs supplémentaires pour être visible dans la grille.

![Espace de connecteur de recherche](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Toutes les suppressions sont souhaitées, puis procédez comme suit :

1. Pour désactiver cette protection temporairement et laisser les suppressions traitée, puis exécutez l’applet de commande PowerShell : `Disable-ADSyncExportDeletionThreshold`. Fournir un compte d’administrateur Global AD Azure et le mot de passe.
![Informations d’identification](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Avec Azure Active Directory Connector toujours sélectionnée, sélectionnez l’action à **exécuter** , puis sélectionnez **Exporter**.
3. Pour réactiver la protection, exécutez l’applet de commande PowerShell : `Enable-ADSyncExportDeletionThreshold`.

## <a name="next-steps"></a>Étapes suivantes

**Rubriques de présentation**

- [Azure AD Connect synchronisation : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
