<properties
    pageTitle="Azure Active Directory Domain Services : Fonctionnalités | Microsoft Azure"
    description="Fonctionnalités des Services de domaine Azure Active Directory"
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
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Services de domaine Active Directory Azure

## <a name="features"></a>Fonctionnalités
Les fonctionnalités suivantes sont disponibles dans les Services de domaine Active Directory Azure gérées domaines.

- **Expérience de déploiement simple :** Vous pouvez activer Azure Active Directory Domain Services pour votre client Azure AD à l’aide de quelques clics. Quelle que soit votre client Azure AD est un client cloud ou qu’ils synchronisés avec votre annuaire local, votre domaine géré peut être mis en service rapidement.

- **Prise en charge de jonction de domaine :** Vous pouvez facilement ordinateurs jonction de domaine dans le réseau virtuel Azure que n’est disponible dans votre domaine géré. L’expérience de jonction de domaine sur Windows client et le fonctionnement de systèmes d’exploitation serveur en toute transparence par rapport aux domaines pris en charge par les Services de domaine Active Directory Azure. Vous pouvez également utiliser jointure domaine automatisé outils auprès de ces domaines.

- **Instance d’un domaine par répertoire Azure AD :** Vous pouvez créer un domaine Active Directory unique pour chaque répertoire Azure AD.

- **Créer des domaines avec des noms personnalisés :** Vous pouvez créer des domaines avec des noms personnalisés (par exemple, « contoso100.com ») à l’aide des Services de domaine Active Directory Azure. Vous pouvez utiliser soit les noms de domaine vérifié ou non vérifié. Si vous le souhaitez, vous pouvez également créer un domaine avec le suffixe de domaine intégré (autrement dit, ' *. onmicrosoft.com') offertes par votre annuaire Azure AD.

- **Intégré à Azure AD :** Vous n’avez pas besoin configurer ou gérer la réplication des Services de domaine Active Directory Azure. Comptes d’utilisateur, les appartenances à un groupe et les informations d’identification (les mots de passe) à partir de votre annuaire Azure AD sont automatiquement disponibles dans Azure Active Directory Domain Services. Nouveaux utilisateurs, des groupes ou des modifications des attributs à partir de votre client Azure AD ou votre annuaire local sont automatiquement synchronisées avec Azure Active Directory Domain Services.

- **NTLM et Kerberos :** Prend en charge l’authentification NTLM et Kerberos, vous pouvez déployer des applications qui s’appuient sur l’authentification Windows intégrée.

- **Utiliser vos informations d’identification/mots de passe d’entreprise :** Les mots de passe pour les utilisateurs de votre client Azure AD fonctionnent avec les Services de domaine Active Directory Azure. Les utilisateurs peuvent utiliser leurs informations d’identification d’entreprise pour machines de jonction de domaine, se connecter de façon interactive ou le contrôle de bureau à distance et le domaine géré s’authentifier.

- **Liaison LDAP et LDAP lire prise en charge :** Vous pouvez utiliser les applications qui dépendent de liaisons LDAP pour authentifier les utilisateurs dans les domaines pris en charge par les Services de domaine Active Directory Azure. En outre, les applications qui utilisent les opérations de lecture LDAP pour interroger les attributs utilisateur/ordinateur à partir du répertoire peuvent également confier contre Azure Active Directory Domain Services.

- **Sécuriser LDAP (LDAPS) :** Vous pouvez activer l’accès au répertoire le protocole LDAP (LDAPS). Un accès sécurisé LDAP est disponible au sein du réseau virtuel par défaut. Toutefois, vous pouvez également activer un accès sécurisé LDAP via internet.

- **La stratégie de groupe :** Vous pouvez utiliser un seul intégré stratégie de groupe chaque pour les utilisateurs et les ordinateurs conteneurs d’être en conformité avec les stratégies de sécurité requises pour les comptes d’utilisateurs et des ordinateurs à un domaine.

- **Gérer le système DNS :** Les membres du groupe « Administrateurs de DC AAD » peuvent gérer le système DNS pour votre domaine géré à l’aide des outils d’administration DNS familiers tels que le composant logiciel enfichable Administration de DNS.

- **Créer personnalisé unités organisationnelles (ou) :** Les membres du groupe « Administrateurs de DC AAD » peuvent créer des unités d’organisation personnalisées dans le domaine géré. Ces utilisateurs disposez des privilèges d’administrateur complets sur les unités d’organisation personnalisées, afin qu’ils peuvent ajouter/supprimer des comptes de service, des ordinateurs, groupes etc. dans ces unités d’organisation personnalisées.

- **Disponible dans plusieurs régions Azure :** Voir la page [services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans laquelle Azure Active Directory Domain Services est disponible.

- **Disponibilité :** Les Services de domaine Active Directory Azure offre haute disponibilité pour votre domaine. Cette fonctionnalité offre la garantie de fonctionnement d’au moins service et tolérance aux pannes. Contrôle des offres d’état intégré automatisée mise à jour des défaillances en faisant pivoter les nouvelles instances pour remplacer les instances échecs et fournir continue du service pour votre domaine.

- **Utiliser les outils de gestion familiers :** Vous pouvez utiliser les outils de gestion Active Directory Windows Server familiers tels que le centre d’administration Active Directory ou Active Directory PowerShell pour gérer des domaines gérés.
