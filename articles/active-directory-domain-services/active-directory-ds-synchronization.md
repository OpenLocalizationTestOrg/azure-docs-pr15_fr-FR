<properties
    pageTitle="Azure Active Directory Domain Services : Synchronisation dans les domaines gérés | Microsoft Azure"
    description="Comprendre la synchronisation dans un domaine géré Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synchronisation dans un domaine géré Azure Active Directory Domain Services
Le diagramme suivant illustre comment fonctionne la synchronisation dans Azure Active Directory Domain Services domaines gérés.

![Topologie de synchronisation dans les Services de domaine Active Directory Azure](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synchronisation à partir de votre annuaire local à votre client Azure AD
Azure AD Connect synchronisation est utilisée pour synchroniser les comptes d’utilisateur, appartenances au groupe, et d’informations d’identification hache à votre client Azure AD. Les attributs d’utilisateur comptes tels que le nom UPN et en local identificateur de sécurité (identificateur de sécurité) sont synchronisés. Si vous utilisez les Services de domaine Active Directory Azure, hachage héritées d’informations d’identification requises pour l’authentification NTLM et Kerberos est également synchronisés avec votre client Azure AD.

Si vous configurez écriture différée, modifications simultanées dans votre annuaire Azure AD sont synchronisées dans vos locaux Active Directory. Par exemple, si vous changez votre mot de passe à l’aide des fonctionnalités de modification de mot de passe libre-service d’Azure AD, le mot de passe modifié est mis à jour dans vos locaux domaine Active Directory.

> [AZURE.NOTE] Toujours utiliser la dernière version d’Azure AD Connect pour vous assurer que vous disposez correctifs pour tous les bogues connus.


## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synchronisation à partir de votre client Azure AD pour votre domaine géré
Comptes d’utilisateurs, appartenances et hachage d’informations d’identification est synchronisés à partir de votre client Azure AD pour votre domaine géré Azure Active Directory Domain Services. Cette procédure de synchronisation est automatique. Vous n’avez pas besoin configurer, surveiller ou gérer cette procédure de synchronisation. Le processus de synchronisation est également unique-way/unidirectionnelle par nature. Votre domaine géré est largement en lecture seule à l’exception de n’importe quel ou personnalisé que vous créez. Par conséquent, vous ne pouvez pas modifier les attributs utilisateur, les mots de passe utilisateur ou appartenance aux groupes dans le domaine géré. Par conséquent, il n’existe aucune synchronisation inversée des modifications à partir de votre domaine géré sur votre client Azure AD.


## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronisation à partir d’une forêt à plusieurs environnement local
De nombreuses organisations ont une infrastructure identité locaux relativement complexe comprenant plusieurs forêts de comptes. Azure AD Connect prend en charge synchronisation des utilisateurs, des groupes et des informations d’identification hachage à partir d’une forêt à plusieurs environnements à votre client Azure AD.

En revanche, votre client Azure AD est une quantité plus simple et plat espace de noms. Pour permettre aux utilisateurs d’accéder de manière fiable applications sécurisées par Azure AD, résoudre les conflits UPN entre des comptes d’utilisateurs dans différentes forêts. Votre ours domaine géré Azure Active Directory Domain Services Fermez ressemblance avec votre client Azure AD. Par conséquent, vous voyez une structure plane à l’unité d’organisation dans votre domaine géré. Tous les utilisateurs et groupes sont stockées dans le conteneur 'AADDC les utilisateurs', quelle que soit la forêt ou le domaine local à partir de laquelle ils ont été synchronisées dans. Il pouvez que vous avez configuré une unité d’organisation hiérarchique structurer en local. Toutefois, votre domaine géré a toujours une structure d’unité d’organisation plate simple.


## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Exclusions - ce qui n’est pas synchronisé pour votre domaine géré
Les objets ou les attributs suivants ne sont pas synchronisés à votre client Azure AD ou à votre domaine géré :

- **Exclus attributs :** Vous pouvez choisir d’exclure certains attributs de se synchroniser avec votre client Azure AD à partir de votre domaine local à l’aide d’Azure AD Connect. Les attributs exclus ne sont pas disponibles dans votre domaine géré.

- **Des stratégies de groupe :** Stratégies de groupe configurés dans votre domaine local ne sont pas synchronisés sur votre domaine géré.

- **Partage SYSVOL :** De même, le contenu du partage SYSVOL sur votre domaine en local n’est pas synchronisé sur votre domaine géré.

- **Objets ordinateur :** Objets ordinateur pour les ordinateurs liés à votre domaine local ne sont pas synchronisés sur votre domaine géré. Ces ordinateurs ne pas avoir une relation d’approbation avec votre domaine géré et appartiennent à votre domaine local uniquement. Dans votre domaine géré, vous recherchez des objets ordinateur uniquement pour les ordinateurs que vous avez explicitement à un domaine pour le domaine géré.

- **Attributs SidHistory pour les utilisateurs et groupes :** L’utilisateur principal et le groupe principal identificateurs de sécurité à partir de votre domaine local sont synchronisés avec votre domaine géré. Toutefois, les attributs SidHistory existants pour les utilisateurs et groupes ne sont pas synchronisés à partir de votre domaine local vers votre domaine géré.

- **Structures d’unités d’organisation (OU) :** Unités d’organisation définies dans votre domaine local ne sont pas synchronisent sur votre domaine géré. Il existe deux unités d’organisation intégrées dans votre domaine géré. Par défaut, votre domaine géré comporte une structure plane à l’unité d’organisation. Vous pouvez toutefois choisir de [créer une unité d’organisation personnalisée dans votre domaine géré](./active-directory-ds-admin-guide-create-ou.md).


## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Comment les attributs spécifiques sont synchronisées avec votre domaine géré
Le tableau suivant répertorie certains attributs courants et décrit comment ils sont synchronisés avec votre domaine géré.

| Dans votre domaine géré d’attribut | Source | Notes |
|:---|:---|:---|
|UPN|Attribut UPN de l’utilisateur de votre client Azure AD|L’attribut UPN à partir de votre client Azure AD est synchronisé que consiste à votre domaine géré. Par conséquent, la méthode la plus fiable à se connecter à votre domaine géré utilise votre nom UPN.|
|SAMAccountName|MailNickname de l’utilisateur de votre client Azure AD d’attribut ou générées automatiquement|L’attribut SAMAccountName est provenant de l’attribut mailNickname dans votre client Azure AD. Si plusieurs comptes d’utilisateurs ont le même attribut mailNickname, SAMAccountName est généré automatiquement. Si l’utilisateur mailNickname ou préfixe UPN n’est plu de 20 caractères, SAMAccountName est généré pour satisfaire la limite de 20 caractères sur les attributs SAMAccountName.|
|Mots de passe|Mot de passe de l’utilisateur à partir de votre client Azure AD|Hachage d’informations d’identification requises pour l’authentification Kerberos ou NTLM (également appelée informations d’identification supplémentaires) est synchronisés à partir de votre client Azure AD. Si votre client Azure AD est un client synchronisé, ces informations d’identification qui proviennent de votre domaine local.|
|Utilisateur/groupe principal identificateur de sécurité|Générées automatiquement|L’identificateur de sécurité principal pour les comptes d’utilisateur/groupe est généré automatiquement dans votre domaine géré. Cet attribut ne correspond pas à l’identificateur de sécurité de l’objet dans vos locaux utilisateur/groupe principal domaine Active Directory. Cette différence est parce que le domaine géré comporte un espace de noms différent de l’identificateur de sécurité à votre domaine local.|
|Historique de l’identificateur de sécurité pour les utilisateurs et groupes|Utilisateur principal local et le groupe identificateur de sécurité|L’attribut SidHistory pour les utilisateurs et groupes dans votre domaine géré est défini pour correspondre à l’utilisateur principal correspondant ou le groupe identificateur de sécurité de votre domaine local. Cette fonctionnalité permet de simplifier levée et MAJ d’applications en local pour le domaine géré, étant donné que vous n’avez pas besoin ressources re et.|

> [AZURE.NOTE] **Se connecter au domaine géré en utilisant le format UPN :** L’attribut SAMAccountName peut être générée automatiquement pour certains comptes d’utilisateurs dans votre domaine géré. Si plusieurs utilisateurs ont le même attribut mailNickname ou les utilisateurs ont plus longs préfixes UPN, SAMAccountName pour ces utilisateurs peut être généré automatiquement. Par conséquent, le format SAMAccountName (par exemple, « CONTOSO100\joeuser ») n’est pas toujours un moyen fiable pour se connecter au domaine. Généré SAMAccountName utilisateurs peut-être différer de leur préfixe UPN. Utilisez le format UPN (par exemple, 'joeuser@contoso100.com') connecter pour le domaine géré fiable.


## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objets qui ne sont pas synchronisés à partir de votre domaine géré à votre client Azure AD
Comme décrit dans une section précédente de cet article, il n’existe aucune synchronisation à partir de votre domaine géré revenir à votre client Azure AD. Vous pouvez choisir pour [créer une unité d’organisation personnalisé (OU)](./active-directory-ds-admin-guide-create-ou.md) dans votre domaine géré. En outre, vous pouvez créer d’autres unités d’organisation, les utilisateurs, des groupes ou des comptes de service au sein de ces unités d’organisation personnalisées. Les objets créés dans les unités d’organisation personnalisées sont synchronisées revenir à votre client Azure AD. Ces objets sont disponibles pour utiliser uniquement au sein de votre domaine géré. Par conséquent, ces objets ne sont pas visibles à l’aide des applets de commande Azure AD PowerShell, API Azure AD graphique ou à l’aide de l’interface utilisateur de gestion Azure AD.


## <a name="related-content"></a>Contenu associé
- [Fonctionnalités - Azure Active Directory Domain Services](active-directory-ds-features.md)

- [Scénarios de déploiement - Services de domaine Active Directory Azure](active-directory-ds-scenarios.md)

- [Considérations relatives à la mise en réseau pour les Services de domaine Active Directory Azure](active-directory-ds-networking.md)

- [Prise en main des Services de domaine Active Directory Azure](active-directory-ds-getting-started.md)
