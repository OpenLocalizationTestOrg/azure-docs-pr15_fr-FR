<properties
    pageTitle="Azure AD Connect synchronisation : comprendre et personnaliser la synchronisation | Microsoft Azure"
    description="Explique comment Azure AD Connect synchroniser works et comment personnaliser."
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
    ms.date="08/29/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect synchronisation : comprendre et personnaliser la synchronisation
Les services de synchronisation Azure Active Directory Connect (synchronisation Azure AD Connect) est un composant principal de Azure AD Connect. Il s’occupe de toutes les opérations associées à synchroniser des données d’identité entre votre environnement local et Azure AD. Azure AD Connect synchronisation est le successeur de synchronisation d’annuaire, la synchronisation Azure Active Directory et Forefront Identity Manager avec Azure Active Directory Connector configuré.

Cette rubrique est la page d’accueil pour la **synchronisation Azure AD Connect** (également appelé **moteur de synchronisation**) et répertorie les liens vers tous les autres rubriques qui lui sont associés. Pour des liens vers Azure AD Connect, voir [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

Le service de synchronisation se compose de deux composants, le composant de **synchronisation Azure AD Connect** en local et le côté de service dans Azure AD appelé **service de synchronisation Azure AD Connect**. Le service est courant pour la synchronisation d’annuaire, Azure AD Sync et Azure AD Connect.

## <a name="azure-ad-connect-sync-topics"></a>Rubriques de synchronisation Azure AD Connect

Rubrique | Il traite et quand lire
----- | -----
**Principes de base de synchronisation Azure AD Connect** |
[Présentation de l’architecture](active-directory-aadconnectsync-understanding-architecture.md) | Pour ceux qui sont nouvelles au moteur de synchronisation et souhaitez en savoir plus sur l’architecture et les termes utilisés.
[Concepts techniques](active-directory-aadconnectsync-technical-concepts.md) | Une version courte de la rubrique architecture et brièvement explique les termes utilisés.
[Se connecter topologies pour Azure AD](active-directory-aadconnect-topologies.md) | Décrit les différentes topologies et les scénarios que prend en charge le moteur de synchronisation.
**Configuration personnalisée** |
[Relancer l’Assistant installation](active-directory-aadconnectsync-installation-wizard.md) | Décrit les options que vous avez disponibles lorsque vous exécutez l’Assistant installation Azure AD Connect à nouveau.
[Présentation de la mise en service déclarative](active-directory-aadconnectsync-understanding-declarative-provisioning.md)| Décrit le modèle de configuration appelé approvisionnement déclarative.
[Présentation des Expressions de mise en service déclaratives](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | Décrit la syntaxe de la langue d’expression utilisée dans la mise en service déclarative.
[Présentation de la configuration par défaut](active-directory-aadconnectsync-understanding-default-configuration.md)| Décrit les règles de prédéfinies et la configuration par défaut. Également décrit comment les règles de travailler ensemble pour les scénarios de prédéfinies pour l’utiliser.
[Contacts et compréhension des utilisateurs](active-directory-aadconnectsync-understanding-users-and-contacts.md) | Continue dans la rubrique précédente et décrit comment la configuration des utilisateurs et des contacts animation, en particulier dans un environnement de forêt multiples.
[Comment effectuer un changement de la configuration par défaut](active-directory-aadconnectsync-change-the-configuration.md) | Décrit comment modifier une configuration courante pour les flux d’attributs.
[Meilleures pratiques pour la modification de la configuration par défaut](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | Prise en charge des limitations et d’apporter des modifications à la configuration prête à l’emploi.
[Configuration du filtrage](active-directory-aadconnectsync-configure-filtering.md) | Décrit les différentes options pour savoir comment limiter les objets sont en cours synchronisées avec Azure AD et étape par étape comment faire pour configurer ces options.
**Fonctionnalités et scénarios** |
[Pas de suppression accidentelle](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | Décrit la fonctionnalité *pas de suppression accidentelle* et comment les configurer.
[Planificateur](active-directory-aadconnectsync-feature-scheduler.md) | Décrit le Planificateur intégré, qui est l’importation, synchroniser et l’exportation de données.
[Mettre en œuvre la synchronisation de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md) | Décrit le fonctionnement de la synchronisation de mot de passe, comment mettre en œuvre et comment permettent d’opérer et résoudre les problèmes.
[Écriture différée appareil](active-directory-aadconnect-feature-device-writeback.md) | Décrit le fonctionne de l’écriture différée appareil dans Azure AD Connect.
[Extensions d’annuaire](active-directory-aadconnectsync-feature-directory-extensions.md) | Décrit comment étendre le schéma Azure AD avec vos propres attributs personnalisés.
**Service de synchronisation** |
[Fonctionnalités du service synchronisation Azure AD Connect](active-directory-aadconnectsyncservice-features.md) | Décrit le côté de service de synchronisation et comment modifier les paramètres de synchronisation dans Azure Active Directory.
[Résilience attribut en double](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) | Décrit comment activer et utiliser la résilience des valeurs en double attribut **userPrincipalName** et **proxyAddresses** .
**Opérations et l’interface utilisateur** |
[Gestionnaire de services de synchronisation](active-directory-aadconnectsync-service-manager-ui.md) | Décrit le Gestionnaire de services de synchronisation UI, y compris les onglets de [Recherche](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) , [connecteurs](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)et [opérations](active-directory-aadconnectsync-service-manager-ui-operations.md).
[Considérations et des tâches opérationnelles](active-directory-aadconnectsync-operations.md) | Décrit les questions opérationnelles, telles que sinistre.
**Comment...** |
[Réinitialiser le compte Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md) | Comment réinitialiser les informations d’identification du compte de service utilisé pour la connexion de synchronisation Azure AD Connect à Azure Active Directory.
**Plus d’informations et des références** |
[Ports](active-directory-aadconnect-ports.md) | Répertorie les ports que vous devez l’ouvrir entre le moteur de synchronisation et vos répertoires en local et Azure AD.
[Les attributs synchronisés à Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) | Répertorie tous les attributs synchronisés entre locaux AD et Azure AD.
[Référence des fonctions](active-directory-aadconnectsync-functions-reference.md) | Répertorie toutes les fonctions disponibles dans la mise en service déclarative.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
