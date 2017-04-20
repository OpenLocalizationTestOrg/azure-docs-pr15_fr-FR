<properties
    pageTitle="Azure AD Connect synchronisation : comment apporter une modification à la configuration par défaut | Microsoft Azure"
    description="Décrit comment effectuer un changement de configuration de synchronisation Azure AD Connect."
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


# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Azure AD Connect synchronisation : comment apporter une modification à la configuration par défaut
L’objectif de cette rubrique consiste à vous expliquant en détail comment apporter des modifications à la configuration par défaut dans Azure AD Connect synchroniser. Il fournit les étapes nécessaires pour certains scénarios courants. Ces connaissances, vous devriez pouvoir apporter certaines modifications simples à votre propre configuration en fonction de vos propres règles d’entreprise.

## <a name="synchronization-rules-editor"></a>Éditeur de règles de synchronisation
L’éditeur de règles de synchronisation est utilisé pour afficher et modifier la configuration par défaut. Vous pouvez trouver dans le Menu Démarrer sous le groupe **Azure AD Connect** .  
![Menu Démarrer avec l’éditeur de règle de synchronisation](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Lorsque vous l’ouvrez, vous voyez les règles de prédéfinies par défaut.

![Éditeur de règles de synchronisation](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigation dans l’éditeur
Les listes déroulantes dans la partie supérieure de l’éditeur permettent de trouver rapidement une règle particulière. Par exemple, si vous voulez voir les règles de l’endroit où l’attribut proxyAddresses est inclus, vous devez modifier les listes déroulantes à ce qui suit :  
![Filtrage SRE](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Pour redéfinir le filtrage et charger une configuration novateur, appuyez sur **F5** sur le clavier.

La partie supérieure droite, vous avez un bouton **Ajouter une nouvelle règle**. Ce bouton est utilisé pour créer votre propre règle personnalisée.

En bas, vous disposez des boutons d’action sur une règle de synchronisation sélectionnée. **Modifier** et **Supprimer** le faire que prévu. **Exporter** génère un script PowerShell pour recréer la règle de synchronisation. Cette procédure vous permet de déplacer une règle de synchronisation d’un serveur vers un autre.

## <a name="create-your-first-custom-rule"></a>Créer votre première règle personnalisée
La modification courante concerne les modifications dans les flux attribut. Les données dans votre annuaire source peuvent ne pas être obtenus Azure AD. Dans l’exemple dans cette section, vous voulez vérifier que le prénom d’un utilisateur est toujours **avec une majuscule**initiale.

### <a name="disable-the-scheduler"></a>Désactiver le planificateur
Le [Planificateur](active-directory-aadconnectsync-feature-scheduler.md) s’exécute toutes les 30 minutes par défaut. Vous voulez vous assurer qu’il ne démarre pas pendant que vous apportez des modifications et résoudre les problèmes de vos nouvelles règles. Pour désactiver temporairement le planificateur, démarrer PowerShell et exécuter`Set-ADSyncScheduler -SyncCycleEnabled $false`

![Désactiver le planificateur](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Créer la règle

1. Cliquez sur **Ajouter une nouvelle règle**.
2. Dans la page **Description** , entrez les informations suivantes :  
![Règle filtrage entrant](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
    - Nom : Donnez la règle un nom descriptif.
    - Description : Clarifier afin que quelqu'un puisse comprendre ce qui concerne la règle.
    - Connectez système : le système peut trouver l’objet dans. Dans ce cas, nous allons sélectionner le connecteur Active Directory.
    - Type d’objet système/métaverse connecté : Sélectionnez **utilisateur** et **personne** respectivement.
    - Type de liaison : Modifier cette valeur à **rejoindre**.
    - Priorité : Fournir une valeur qui est unique dans le système. Une valeur numérique inférieure indique une priorité plus élevée.
    - Balise : Laissez vide. Uniquement les règles de prédéfinies de Microsoft doivent avoir cette case renseignée à l’aide d’une valeur.
3. Dans la page **Scoping filtre** , entrez le **prénom ISNOTNULL**.  
![Règle étendue filtre de trafic entrant](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
Cette section permet de définir les objets de que la règle doit s’appliquer à. Si laissé vide, la règle s’appliquera à tous les objets utilisateur. Mais qui inclut des salles de conférence, les comptes de service et les autres objets utilisateur non personnes.
4. Sur la **participation à des règles**, laissez-la vide.
5. Dans la page **Transformations** , remplacez le TauxChange du **Expression**. Sélectionnez l' attribut cible **prénom**, puis dans la Source entrez `PCase([givenName])`.
![Règle de trafic entrant transformations](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
Le moteur de synchronisation respecte la casse à la fois sur le nom de la fonction et le nom de l’attribut. Si vous tapez un problème, vous voyez un avertissement s’affiche lorsque vous ajoutez la règle. L’éditeur vous permet d’enregistrer et de poursuivre, afin que vous devez rouvrir la règle et corrigez la règle.
6. Cliquez sur **Ajouter** pour enregistrer la règle.

Votre nouvelle règle personnalisée doit être visible par les autres règles de synchronisation dans le système.

### <a name="verify-the-change"></a>Vérifiez que la modification
Avec cette nouvelle modification, que vous voulez qu’il fonctionne comme prévu et n’est pas lever des erreurs. Selon le nombre d’objets que vous avez, il existe deux manières d’effectuer cette étape.

1. Exécuter une synchronisation complète sur tous les objets
2. Exécuter un aperçu et la synchronisation complète sur un seul objet

Démarrer le **Service de synchronisation** dans le menu Démarrer. Les étapes décrites dans cette section présentent tous cet outil.

1. **Synchronisation complète sur tous les objets**  
Sélectionnez les **liens** dans la partie supérieure. Identifier le lien que vous avez apporté une modification à dans la section précédente, dans ce cas, les Services de domaine Active Directory, et sélectionnez-le. Sélectionnez **exécuter** à partir de Actions, **Synchronisation complète** et **OK**.
![Synchronisation complète](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
Les objets sont désormais mis à jour dans le méta-verse. Vous voulez consulter l’objet dans le méta-verse.

2. **Afficher un aperçu et synchronisation complète sur un seul objet**  
Sélectionnez les **liens** dans la partie supérieure. Identifier le lien que vous avez apporté une modification à dans la section précédente, dans ce cas, les Services de domaine Active Directory, et sélectionnez-le. Sélectionnez **l’espace de connecteur de recherche**. Étendue permet de rechercher un objet que vous souhaitez utiliser pour tester la modification. Sélectionnez l’objet, puis cliquez sur **Aperçu**. Dans l’écran Nouveau, sélectionnez **Valider l’aperçu**.
![Valider preview](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
La modification s’engage au métaverse.

**Consulter l’objet dans le méta-verse**  
Vous voulez maintenant choisir quelques exemples d’objets pour vous assurer que la valeur est attendue et que la règle est appliquée. Sélectionnez la **Recherche** à partir du haut. Ajouter un filtre pour trouver les objets pertinents. Résultats de la recherche, ouvrez un objet. Examiner les valeurs des attributs et vérifiez également dans la colonne **Règles de synchronisation** que la règle soit appliquée comme prévue.  
![Recherche](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  
### <a name="enable-the-scheduler"></a>Activer le planificateur
Si tout est comme prévu, vous pouvez activer à nouveau le planificateur. À partir de PowerShell, exécutez `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Les autres modifications de flux attribut courantes
La section précédente décrit comment apporter des modifications à un flux d’attribut. Dans cette section, quelques exemples supplémentaires sont fournis. Sous forme abrégée les étapes pour créer la règle de synchronisation, mais vous pouvez trouver les étapes complètes de la section précédente.

### <a name="use-another-attribute-than-the-default"></a>Utiliser un autre attribut que celui par défaut
Chez Fabrikam, il existe une forêt où l’alphabet local est utilisé pour Prénom, nom et nom d’affichage. La représentation des caractères latins de ces attributs sont accessibles dans les attributs d’extension. Lors de la création de la liste d’adresses globale dans Azure AD et Office 365, l’organisation souhaite les attributs à la place.

Avec une configuration par défaut, un objet de la forêt locale ressemble à ceci :  
![Flux des attributs 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Pour créer une règle avec les flux d’autres attributs, procédez comme suit :

- Démarrez **l’Éditeur de règle de synchronisation** dans le menu Démarrer.
- **Entrant** toujours sélectionnée vers la gauche, cliquez sur le bouton **Ajouter une nouvelle règle**.
- Attribuer un nom et une description à la règle. Sélectionnez Active Directory local et les types d’objets pertinents.  Dans **Type de liaison**, sélectionnez **rejoindre**. Pour priorité, choisissez un numéro qui n’est pas utilisé par une autre règle. Les règles prédéfinies de commencent par 100, la valeur 50 peut être utilisée dans cet exemple.
![Flux des attributs 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- Laissez l’étendue vide (autrement dit, doit être appliqué à tous les objets utilisateur de la forêt).
- Laissez les règles de jointure vide (autrement dit, vous permettent de la règle prédéfinies de gérer les jointures).
- Dans les Transformations, créez les flux suivants :  
![Flux des attributs 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- Cliquez sur **Ajouter** pour enregistrer la règle.
- Accédez au **Gestionnaire de services de synchronisation**. Sur les **connecteurs**, sélectionnez le lien dans lequel nous avons ajouté la règle. Sélectionnez **exécuter**et **synchronisation complète**. Une synchronisation complète recalcule tous les objets en utilisant les règles en cours.

C’est le résultat pour le même objet avec cette règle personnalisée :  
![Flux des attributs 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Longueur des attributs
Attributs de chaîne sont par défaut définie doivent être indexés et la longueur maximale est 448 caractères. Si vous travaillez avec des attributs de chaîne qui peuvent contenir plus, vérifiez qu’inclure les éléments suivants dans le flux des attributs :  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>Modification de l’userPrincipalSuffix
L’attribut userPrincipalName dans Active Directory n’est pas toujours connu par les utilisateurs et peut-être pas adaptée en tant que l’ID de connexion. La Azure AD Connect synchroniser l’Assistant installation permet en sélectionnant un attribut différent, par exemple courrier. Mais dans certains cas, l’attribut doit être calculé. Par exemple, l’entreprise Contoso comporte deux répertoires Azure AD, une pour production et une pour le test. Ils souhaitent les utilisateurs de leur client test pour utiliser un autre suffixe dans l’ID de connexion.  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

Dans cette expression, prendre tout à gauche de la première @-sign (Word) et concatène avec une chaîne fixe.

### <a name="convert-a-multi-value-to-a-single-value"></a>Convertir un à plusieurs valeurs à une valeur unique
Certains attributs dans Active Directory sont à valeurs multiples dans le schéma de bien qu’ils ressemblent unique évalué aux utilisateurs Active Directory et ordinateurs. L’attribut description est un exemple.  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

Dans cette expression au cas où l’attribut comporte une valeur, nous prendre le premier élément (élément) dans l’attribut, supprimer les espaces de tête et (Trim) et puis conserver les premières 448 caractères (à gauche) dans la chaîne.

### <a name="do-not-flow-an-attribute"></a>Ne passent pas d’un attribut
Pour des informations générales sur le scénario de cette section, voir [contrôler le processus de flux attribut](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Il existe deux façons de se déplace ne pas un attribut. La première est disponible dans l’Assistant installation et vous permet de [Supprimer les attributs sélectionnés](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Cette option est disponible si vous n’avez jamais synchronisé l’attribut avant. Toutefois, si vous avez commencé à synchroniser cet attribut et supprimer ultérieurement ces derniers avec cette fonctionnalité, les taquets de moteur de synchronisation gestion de l’attribut et les valeurs existantes restent dans Azure Active Directory.

Si vous souhaitez supprimer la valeur d’un attribut et vérifiez qu’il n’est pas transmis à l’avenir, vous devez créer une règle personnalisée à la place.

Chez Fabrikam, nous avons réalisé que certains des attributs que nous synchroniser dans le cloud ne doivent pas être il. Nous voulons pour vous assurer que les attributs sont supprimés à partir d’Azure AD.  
![Attributs d’Extension incorrecte](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- Créer une nouvelle règle de synchronisation entrants et remplir la description ![Descriptions](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
- Créer des flux attribut du type **Expression** et avec la source **AuthoritativeNull**. valeur littérale **AuthoritativeNull** indique que la valeur doit être vide dans la MV même si une règle de synchronisation de priorité inférieure essaie de remplir la valeur.
![Transformation pour les attributs d’Extension](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- Enregistrer la règle de synchronisation. Démarrer **Le Service de synchronisation**, trouver le connecteur, sélectionnez **exécuter**et **Synchronisation complète**. Cette étape recalcule tous les flux d’attribut.
- Vérifiez que les modifications souhaitées sont sur le point d’être exportés en recherchant dans l’espace de connecteur.
![Supprimer intermédiaire](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le modèle de configuration de [Mise en service déclarative compréhension](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- En savoir plus sur le langage d’expression dans les [Expressions de mise en service déclarative compréhension](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Rubriques de présentation**

- [Azure AD Connect synchronisation : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
