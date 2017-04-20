<properties
    pageTitle="Azure AD Connect synchronisation : concepts techniques | Microsoft Azure"
    description="Explique les concepts techniques de synchronisation Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-technical-concepts"></a>Azure AD Connect synchronisation : Concepts techniques
Cet article est un résumé de la rubrique [Présentation de l’architecture](active-directory-aadconnectsync-technical-concepts.md).

Azure AD Connect synchronisation s’appuie sur une plateforme de synchronisation du méta-annuaire Uni.
Les sections suivantes présentent les concepts de synchronisation du méta-annuaire.
S’appuyant sur MIIS, ILM et FIM, les Services de synchronisation Azure Active Directory fournit la plateforme suivante pour la connexion aux sources de données, synchroniser des données entre des sources de données, ainsi que la mise en service et hors service des identités.

![Concepts techniques](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

Les sections suivantes fournissent plus d’informations sur les aspects suivants du Service de synchronisation FIM :

- Connecteur
- Flux des attributs
- Espace de connecteur
- Métaverse
- Mise en service

## <a name="connector"></a>Connecteur

Les modules de code qui sont utilisés pour communiquer avec un annuaire connecté sont appelés liens (auparavant appelés agents de gestion (MAs)).

Ils sont installés sur l’ordinateur qui exécute synchronisation Azure AD Connect.
Les connecteurs permettent sans agent converser à l’aide des protocoles système distant plutôt que d’utiliser le déploiement d’agents spécialisés. Cela signifie une réduction des risques et les heures de déploiement, en particulier lorsqu’il s’agit des applications critiques et systèmes.

Dans l’image ci-dessus, le connecteur est synonyme de l’espace de connecteur mais englobe toutes les communications avec le système externe.

Le connecteur est responsable de tous les importer et exporter des fonctionnalités dans le système et libère les développeurs d’avoir à comprendre comment se connecter à chaque système en mode natif lors de l’utilisation de la mise en service déclarative pour personnaliser des transformations de données.

Importation et exportation ne se produire lors de la planification, permettant d’approfondir isolation des modifications simultanées au sein du système, étant donné que les modifications ne sont pas automatiquement propagent à la source de données connectées. En outre, les développeurs peuvent également créer leurs propres connecteurs pour vous connecter à pratiquement n’importe quelle source de données.

## <a name="attribute-flow"></a>Flux des attributs

Le méta-verse est la vue d’ensemble de toutes les identités jointes des voisines espaces connecteur. Dans l’illustration ci-dessus, flux des attributs est représenté par des lignes avec flèches pour le flux entrant et sortant. Flux des attributs est le processus de copie ou la transformation de données d’un système à l’autre et toutes les attribut flux (entrants ou sortants).

Flux des attributs se produit entre l’espace de connecteur et le métaverse bidirectionnelle synchronisation (complet ou delta) opérations sont planifiées à exécuter.

Flux des attributs se produit uniquement lorsque ces synchronisations sont exécutent. Les flux d’attributs sont définis dans les règles de synchronisation. Il peut s’agir (ISR dans l’image ci-dessus) entrant ou sortant (OSR dans l’image ci-dessus).

## <a name="connected-system"></a>Système connecté

Système connecté (aka annuaire connecté) est faisant référence au système distant Azure AD Connect synchronisation s’est connecté à et lire et écrire des données d’identité vers et depuis.

## <a name="connector-space"></a>Espace de connecteur

Chaque source de données connectée est représenté par un sous-ensemble filtré des objets et des attributs de l’espace connecteur.
Cela permet au service de synchronisation de fonctionner localement sans avoir à contacter le système distant lors de la synchronisation des objets et limite l’interaction à l’importation et exporte uniquement.

Lorsque la source de données et le connecteur ont la possibilité de fournir une liste des modifications (une importation delta), puis l’efficacité opérationnelle augmente considérablement en tant que seules les modifications apportées depuis le dernier cycle d’interrogation échangés. L’espace de connecteur protège la source de données connectées à partir de se propager automatiquement en demandant que le calendrier de connecteur importe et exporte les modifications. Cette assurance ajoutée accorde toute sérénité lors de test, l’aperçu ou confirmation de la prochaine mise à jour.

## <a name="metaverse"></a>Métaverse

Le méta-verse est la vue d’ensemble de toutes les identités jointes des voisines espaces connecteur.

Comme identités sont liées entre eux et autorité est affectée pour divers attributs à importer des mappages de flux, l’objet du métaverse centrale commence pour regrouper les informations à partir de plusieurs systèmes. À partir de ce flux des attributs objet, mappages comportent des informations aux systèmes sortants.

Objets sont créés lorsqu’un système faisant autorité les projets dans le métaverse. Dès que toutes les connexions sont supprimées, l’objet du métaverse est supprimé.

Objets du métaverse ne peuvent pas être modifiés directement. Toutes les données de l’objet doivent être contribuées via le flux de l’attribut. Le méta-verse conserve les connecteurs permanentes avec chaque espace connecteur. Ces liens ne nécessitent pas de réévaluation pour chaque synchronisation exécuter. Cela signifie qu’Azure AD Connect synchronisation ne comporte pas de localiser l’objet à distance correspondant chaque fois. Cela permet d’éviter la nécessité d’agents coûteuses pour empêcher les modifications dans les attributs qui seraient normalement responsables de corrélation entre les objets.

Lors de la découverte de nouvelles sources de données qui peuvent avoir des objets préexistants qui doivent être gérés, synchronisation Azure AD Connect utilise un processus appelé une règle de jointure pour évaluer les candidats potentiels avec lesquelles établir un lien.
Une fois que le lien est établi, cette évaluation ne se reproduise et flux des attributs normal peut se produire entre la source de données connectées à distance et le métaverse.

## <a name="provisioning"></a>Mise en service

Lorsqu’un projets source faisant autorité un nouvel objet dans le métaverse un nouvel objet espace connecteur peut être créé dans un autre Connector représentant une source de données connectée en aval.

Cela crée, par nature, un lien et flux des attributs pouvez passer en mode bidirectionnel.

Chaque fois qu’une règle détermine qu’un nouvel objet espace connecteur doit être créé, on parle de mise en service. Toutefois, étant donné que cette opération est limitée à l’espace de connecteur, il ne comporte pas dans la source de données connectées jusqu'à ce qu’une exportation est effectuée.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Synchronisation Azure Active Directory se connecter : Options de personnalisation de la synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
