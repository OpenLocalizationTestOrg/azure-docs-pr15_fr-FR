<properties
    pageTitle="Azure AD Connect synchronisation : exécuter l’Assistant installation une deuxième fois | Microsoft Azure"
    description="Décrit le fonctionne de l’Assistant installation sur la deuxième fois que vous exécutez."
    keywords="L’Assistant installation Azure AD Connect vous permet de configurer les paramètres de maintenance la deuxième fois que vous l’exécuter"
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
    ms.date="08/31/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect synchronisation : exécuter l’Assistant installation une deuxième fois
La première fois que vous exécutez l’Assistant installation Azure AD Connect, il vous guide dans la configuration de votre installation. Si vous réexécutez l’Assistant installation, il propose des options de maintenance.

Vous pouvez trouver l’Assistant installation dans le menu Démarrer nommé **Azure AD Connect**.

![Menu Démarrer](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Lorsque vous démarrez l’Assistant installation, vous voyez une page avec les options suivantes :

![Page contenant la liste des tâches supplémentaires](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Si vous avez installé ADFS avec Azure AD Connect, vous avez plus d’options. Les options supplémentaires pour ADFS sont présentées dans la [Gestion des services ADFS](active-directory-aadconnect-federation-management.md#ad-fs-management).

Sélectionnez une des tâches, puis cliquez sur **suivant** pour continuer.

> [AZURE.IMPORTANT] Tandis que l’Assistant installation est ouvert, toutes les opérations dans le moteur de synchronisation sont suspendues. Vérifiez que vous fermez l’Assistant installation dès que vous avez terminé vos modifications de configuration.

## <a name="view-current-configuration"></a>Afficher la configuration en cours
Cette option vous offre une vue rapide de vos options actuellement configurées.

![Page avec une liste de toutes les options et leur état](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Cliquez sur **précédent** pour revenir en arrière. Si vous sélectionnez **Quitter**, vous fermez l’Assistant installation.

## <a name="customize-synchronization-options"></a>Personnaliser les options de synchronisation
Cette option est utilisée pour apporter des modifications à la configuration de la synchronisation. Vous voyez un sous-ensemble des options dans le chemin d’installation de configuration personnalisée. Vous voyez cette option même si vous avez utilisé initialement installation expresse.

- [Ajouter des répertoires plus](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Pour la suppression d’un répertoire, voir [Supprimer un lien](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
- [Changer le domaine et le filtrage de l’unité d’organisation](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
- Supprimer le filtre de groupe.
- [Fonctionnalités facultatives modifier](active-directory-aadconnect-get-started-custom.md#optional-features).

Les autres options de l’installation initiale ne sont pas modifiables et ne sont pas disponibles. Ces options sont :

- Modifiez l’attribut à utiliser pour userPrincipalName et sourceAnchor.
- Modifier la méthode de jonction pour les objets de forêt différente.
- Activer le filtrage basé sur le groupe.

## <a name="refresh-directory-schema"></a>Actualiser le schéma d’annuaire
Cette option est utilisée si vous avez modifié le schéma dans un de vos localement forêts AD DS. Par exemple, peut avoir installé Exchange ou mis à niveau vers un schéma de Windows Server 2012 avec les objets du périphérique. Dans ce cas, vous devez indiquer à Azure AD Connect à nouveau de lire le schéma de domaine Active Directory et de mettre à jour son cache. Cette action régénère également les règles de synchronisation. Si vous ajoutez le schéma Exchange, par exemple, les règles de synchronisation pour Exchange sont ajoutés à la configuration.

Lorsque vous sélectionnez cette option, tous les répertoires dans votre configuration sont répertoriés. Vous pouvez conserver le paramètre par défaut et actualiser toutes les forêts ou annuler la sélection de certains d'entre eux.

![Page avec une liste de tous les répertoires dans l’environnement](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Configurer le mode de mise en attente
Cette option permet d’activer et désactiver le mode intermédiaire sur le serveur. Vous trouverez plus d’informations sur les intermédiaires mode et comment elle est utilisée dans les [opérations](active-directory-aadconnectsync-operations.md#staging-mode).

L’option indique si la mise en attente est actuellement activé ou désactivé :  
![Option qui s’affiche également l’état actuel du mode mise en attente](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Pour modifier l’état, sélectionnez cette option et sélectionner ou désélectionner la case à cocher.  
![Option qui s’affiche également l’état actuel du mode mise en attente](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Modifier la connexion utilisateur
Cette option permet de modifier à partir de la synchronisation de mot de passe pour la fédération ou vice versa. Vous ne pouvez pas convertir **n’est pas configuré**.

Pour plus d’informations sur cette option, voir [connexion utilisateur](active-directory-aadconnect-user-signin.md#changing-user-sign-in-method).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le modèle de configuration utilisé par synchronisation Azure AD Connect au sein de [Comprendre la mise en service déclarative](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

**Rubriques de présentation**

- [Azure AD Connect synchronisation : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
