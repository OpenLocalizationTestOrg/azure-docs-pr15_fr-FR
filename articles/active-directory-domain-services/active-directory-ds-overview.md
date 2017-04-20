<properties
    pageTitle="Vue d’ensemble des Services de domaine Azure Active Directory | Microsoft Azure"
    description="Vue d’ensemble des Services de domaine Azure Active Directory"
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

## <a name="overview"></a>Vue d’ensemble
Azure Infrastructure Services permettent de déployer un large éventail de solutions informatiques de façon flexible. Avec le Machines virtuelles Azure, vous pouvez déployer presque instantanément et vous payez uniquement par les minutes. À l’aide de prise en charge pour Windows, Linux, SQL Server, Oracle, IBM, SAP et BizTalk, vous pouvez déployer les charges de travail, n’importe quelle langue, sur pratiquement n’importe quel système d’exploitation. Ces avantages permettent de migrer des applications héritées déployées local vers Azure, pour enregistrer les dépenses opérationnelles.

Un aspect clé de migration d’applications en local vers Azure gère les besoins de l’identité de ces applications. Applications orientées annuaire peuvent s’appuient sur LDAP pour l’accès en lecture ou écriture à l’annuaire de l’entreprise ou s’appuient sur Windows intégrée (authentification Kerberos ou NTLM) pour l’authentification des utilisateurs finaux. Métier des applications métier s’exécutant sur Windows Server sont généralement déployées sur les ordinateurs joint au domaine, afin qu’ils peuvent être gérés en toute sécurité à l’aide de la stratégie de groupe. Pour les applications 'levée et MAJ' locaux dans le cloud, ces dépendances sur l’infrastructure d’identité de l’entreprise doivent être résolues.

Les administrateurs activer souvent à l’une des solutions suivantes pour répondre aux besoins de leurs applications déployées dans Azure identité :

- Déploiement d’une connexion VPN de site à entre les charges de travail en cours d’exécution dans Azure Infrastructure Services et de l’annuaire de l’entreprise en local.
- Étendre l’infrastructure de domaine/forêt AD d’entreprise en définissant un contrôleur de domaine réplica à l’aide de machines virtuelles Azure.
- Déployer un domaine autonome dans Azure utiliserez contrôleur de domaine déployé en tant que machines virtuelles Azure.

Toutes ces approches souffrent de coût élevé et la charge administrative. Les administrateurs requis pour le déploiement de domaine à l’aide de machines virtuelles dans Azure. En outre, ils doivent gérer, sécuriser, correctif, surveiller, sauvegarder et résoudre les problèmes de ces machines virtuelles. La dépendance sur des connexions VPN à l’annuaire local entraîne déployées dans Azure être concernés par des problèmes réseau temporaires ou défaillances les charges de travail. Ces pannes réseau entraînent à son tour disponibilité inférieure et baisse de fiabilité de ces applications.

Nous avons conçu Azure Active Directory Domain Services pour fournir une autre plus facilement.


## <a name="introducing-azure-ad-domain-services"></a>Présentation des Services de domaine Active Directory Azure
Services de domaine Active Directory Azure fournit des services de domaine géré tels que la jointure de domaines, l’authentification Kerberos/NTLM groupe stratégie, LDAP, qui sont entièrement compatibles avec Active Directory Windows Server. Vous pouvez utiliser ces services de domaine sans avoir besoin de vous permettant de déployer, gérer et correctifs contrôleurs de domaine dans le cloud. Services de domaine Active Directory Azure s’intègre à votre client Azure AD existant, ce qui rend les utilisateurs peuvent se connecter à l’aide de leurs informations d’identification d’entreprise. En outre, vous pouvez utiliser des comptes d’utilisateurs et groupes existants pour sécuriser l’accès aux ressources, garantissant ainsi une plus lisse 'levée-et-MAJ' des ressources locales Azure Infrastructure Services.

Fonctionnalité de Services de domaine Active Directory Azure fonctionne en toute transparence quel que soit votre client Azure AD exclusivement le nuage ou synchronisé avec Active Directory local.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Services de domaine Active Directory Azure pour les organisations exclusivement le nuage
Cloud seule client Azure AD (souvent appelée « clients gérées ») n’a pas aucun encombrement sur l’identité en local. En d’autres termes, les comptes d’utilisateurs, leurs mots de passe et l’appartenance aux groupes est tout cela étant natif dans le cloud - autrement dit, créées et gérées dans Azure AD. Prendre en compte pour un instant que Contoso est cloud seule client Azure AD. Comme le montre l’illustration suivante, l’administrateur de Contoso a configuré un réseau virtuel dans Azure Infrastructure Services. Applications et charges de travail sont déployés sur ce réseau virtuel dans Azure machines virtuelles. Dans la mesure où Contoso est un client exclusivement le nuage, toutes les identités utilisateur, ses informations d’identification et appartenances sont créées et gérées dans Azure Active Directory.

![Vue d’ensemble des Services de domaine Active Directory Azure](./media/active-directory-domain-services-overview/aadds-overview.png)

Contoso administrateur informatique peut activer les Services de domaine Active Directory Azure pour leur client Azure AD et choisir de rendre les services de domaine disponibles dans ce réseau virtuel. Par la suite, les Services de domaine Active Directory Azure dispositions un domaine géré et rend disponible dans le réseau virtuel. Tous les comptes d’utilisateurs, appartenances et informations d’identification utilisateur disponibles dans le client de Azure Active Directory de Contoso sont également disponibles dans ce domaine nouvellement créé. Cette fonctionnalité permet aux utilisateurs de l’organisation à se connecter au domaine avec leurs références d’entreprise - par exemple, lorsque vous vous connectez à distance aux machines à un domaine via le Bureau à distance. Les administrateurs peuvent mise en service de l’accès aux ressources dans le domaine à l’aide d’appartenances existant. Applications déployées dans des machines virtuelles sur le réseau virtuel peuvent utiliser des fonctionnalités telles que la jointure de domaines, LDAP en lecture, LDAP liaison, NTLM et Kerberos et stratégie de groupe.

Quelques aspects utiles du domaine géré est mis en service par les Services de domaine Active Directory Azure sont les suivantes :

- Contoso administrateur informatique n’a pas besoin gérer, correctif ou contrôler ce domaine ou aucun contrôleur de domaine pour ce domaine géré.
- Il n’est pas nécessaire pour gérer la réplication AD pour ce domaine. Comptes d’utilisateurs, appartenances et informations d’identification de client de Azure Active Directory de Contoso sont automatiquement disponibles dans ce domaine géré.
- Étant donné que le domaine est géré par Azure Active Directory Domain Services, Contoso administrateur informatique ne dispose pas des privilèges administrateur de domaine ou d’administrateur d’entreprise sur ce domaine.


### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Services de domaine Active Directory Azure pour les organisations hybride
Organisations avec un environnement hybride infrastructure informatique consomment un mélange de ressources de cloud et en local. Ces organisations synchroniser les informations d’identité de leur répertoire local à leur client Azure AD. Comme organisations hybride Rechercher pour effectuer une migration plus de leurs applications en local dans le cloud, en particulier les applications orientées annuaire hérités, Azure Active Directory Domain Services peuvent être utiles pour les.

Litware Corporation a déployé [Azure AD Connect](../active-directory/active-directory-aadconnect.md), pour synchroniser les informations d’identité à partir de leur répertoire local à leur client Azure AD. Les informations d’identité qui sont synchronisées incluent des comptes d’utilisateurs, leur hachage d’informations d’identification pour l’authentification (synchronisation de mot de passe) et les membres du groupe.

> [AZURE.NOTE] **La synchronisation de mot de passe est obligatoire pour les organisations hybride utiliser les Services de domaine Active Directory Azure**. Cette condition est parce que les informations d’identification des utilisateurs sont nécessaires dans le domaine géré fourni par les Services de domaine Active Directory Azure, pour authentifier ces utilisateurs via les méthodes d’authentification NTLM ou Kerberos.

![Azure Active Directory Domain Services pour Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

L’illustration précédente montre comment les organisations avec un déploiement de l’infrastructure informatique tels que Litware Corporation, peuvent utiliser les Services de domaine Active Directory Azure. Charges de travail nécessitant des services de domaine et les applications de Litware sont déployés dans un réseau virtuel dans Azure Infrastructure Services. De Litware administrateur informatique peut activer les Services de domaine Active Directory Azure pour leur client Azure AD et choisir de rendre un domaine géré disponibles dans ce réseau virtuel. Litware étant une organisation avec une infrastructure informatique hybride, les informations d’identification, des groupes et des comptes d’utilisateurs sont synchronisées avec leur client Azure AD à partir de leur répertoire local. Cette fonctionnalité permet aux utilisateurs de se connecter au domaine avec leurs références d’entreprise - par exemple, lors de la connexion à distance à des ordinateurs joints au domaine via le Bureau à distance. Les administrateurs peuvent mise en service de l’accès aux ressources dans le domaine à l’aide d’appartenances existant. Applications déployées dans des machines virtuelles sur le réseau virtuel peuvent utiliser des fonctionnalités telles que la jointure de domaines, LDAP en lecture, LDAP liaison, NTLM et Kerberos et stratégie de groupe.

Quelques aspects utiles du domaine géré est mis en service par les Services de domaine Active Directory Azure sont les suivantes :

- Le domaine géré est un domaine autonome. Il n’est pas une extension de domaine local de Litware.
- De Litware administrateur informatique n’a pas besoin gérer, correctif, ou contrôler contrôleur de domaine pour ce domaine géré.
- Il n’est pas nécessaire pour gérer la réplication AD pour ce domaine. Comptes d’utilisateur, les appartenances à un groupe et les informations d’identification à partir du répertoire local de Litware sont synchronisées avec Azure AD via Azure AD Connect. Ces comptes d’utilisateur, les appartenances à un groupe et les informations d’identification sont automatiquement disponibles dans le domaine géré.
- Étant donné que le domaine est géré par Azure Active Directory Domain Services, Litware administrateur informatique ne dispose pas des privilèges administrateur de domaine ou d’administrateur d’entreprise sur ce domaine.


## <a name="benefits"></a>Avantages
Avec les Services de domaine Active Directory Azure, vous pouvez tirer parti les avantages suivants :

-   **Simple** – vous pouvez répondre aux besoins identité des machines virtuelles déployés aux services d’Azure Infrastructure en quelques clics simples. Vous n’avez pas besoin déployer et gérer infrastructure d’identité de connectivité Azure ou de configuration revenir à votre infrastructure d’identité local.

-   **Intégré** – Azure Active Directory Domain Services est fortement intégré à votre client Azure AD. Vous pouvez désormais utiliser Azure AD comme un annuaire d’entreprise sur le nuage intégrée qui convient aux besoins de vos applications modernes et les applications orientées annuaire traditionnelles.

-   **Compatible** – Azure Active Directory Domain Services est basé sur l’infrastructure de qualité entreprise de Windows Server Active Directory. Par conséquent, vos applications peuvent s’appuyer sur une plus grande compatibilité avec les fonctionnalités Windows Server Active Directory. Pas toutes les fonctionnalités disponibles dans Windows Server AD sont actuellement disponibles dans Azure Active Directory Domain Services. Toutefois, les fonctionnalités disponibles sont compatibles avec les fonctionnalités Windows Server AD correspondantes que dépendent de votre infrastructure en local. Les fonctions de jointure LDAP, Kerberos, NTLM, stratégie de groupe et le domaine constituent une offre mature qui a été testée et affinée différentes versions de Windows Server.

-   **Rentables** – avec les Services de domaine Active Directory Azure, vous pouvez éviter les tâches infrastructure et gestion, qui sont associé à la gestion de l’infrastructure d’identité pour prendre en charge les applications orientées annuaire traditionnelles. Vous pouvez déplacer ces applications Azure Infrastructure Services et bénéficier d’économies substantielles sur les frais de fonctionnement.
