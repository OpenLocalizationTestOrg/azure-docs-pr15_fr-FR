<properties
    pageTitle="Les Services de domaine Active Directory Azure : Comparer Azure Active Directory Domain Services risquez utilitaire | Microsoft Azure"
    description="Comparaison d’Azure Active Directory Domain Services risquez utilitaire"
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
    ms.date="10/01/2016"
    ms.author="maheshu"/>

# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Comment déterminer si les Services de domaine Active Directory Azure est adaptée à votre cas d’utilisation
Services de domaine Active Directory Azure vous permet de déployer vos charges de travail dans Azure Infrastructure Services, sans avoir à vous soucier de maintenance de votre infrastructure d’identité. Ce service géré est différent à partir d’un déploiement Active Directory Windows Server standard que vous déployez et gérez vos propres. Le service est conçu pour facilité de déploiement, le contrôle d’état automatisé et mise à jour et une infrastructure d’identité simple pour le cloud. Nous sommes constamment évolution du service pour prendre en charge les scénarios de déploiement courants.

Décider si vous voulez utiliser les Services de domaine Active Directory Azure ou tourner et gérer votre propre infrastructure AD (personnalisables) dans Azure :

- Consultez la liste des [fonctionnalités proposées par les Services de domaine Active Directory Azure](active-directory-ds-features.md).

- Passez en revue courants [scénarios de déploiement d’Azure Active Directory Domain Services](active-directory-ds-scenarios.md).

- Pour finir, [comparer les Services de domaine Active Directory Azure à une option AD personnalisables](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).


## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Comparer les Services de domaine Active Directory Azure au domaine AD utilitaire dans Azure
Le tableau suivant vous permet de choisir entre l’utilisation des Services de domaine Active Directory Azure et la gestion de votre propre infrastructure AD dans Azure.

|**Fonctionnalité**|**Services de domaine Active Directory Azure**|**« Personnalisables » AD dans machines virtuelles Azure**|
|---|:---:|:---:|
|[**Service géré**](active-directory-ds-comparison.md#managed-service)|**& #x 2713 ;**|**& #x 2715 ;**|
|[**Sécuriser les déploiements**](active-directory-ds-comparison.md#secure-deployments)|**& #x 2713 ;**|Administrateur doit sécuriser le déploiement.|
|[**Serveur DNS**](active-directory-ds-comparison.md#dns-server)|**& #x 2713 ;** (service géré)|**& #x 2713 ;**|
|[**Privilèges administrateur d’entreprise ou de domaine**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges)|**& #x 2715 ;**|**& #x 2713 ;**|
|[**Jonction de domaine**](active-directory-ds-comparison.md#domain-join)|**& #x 2713 ;**|**& #x 2713 ;**|
|[**Authentification de domaine à l’aide de NTLM et Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos)|**& #x 2713 ;**|**& #x 2713 ;**|
|[**Structure d’unité d’organisation personnalisée**](active-directory-ds-comparison.md#custom-ou-structure)|**& #x 2713 ;**|**& #x 2713 ;**|
|[**Extensions de schéma**](active-directory-ds-comparison.md#schema-extensions)|**& #x 2715 ;**|**& #x 2713 ;**|
|[**Approuve le domaine/forêt Active Directory**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts)|**& #x 2715 ;**|**& #x 2713 ;**|
|[**Lecture LDAP**](active-directory-ds-comparison.md#ldap-read)|**& #x 2713 ;**|**& #x 2713 ;**|
|[**LDAP sécurisé (LDAPS)**](active-directory-ds-comparison.md#secure-ldap)|**& #x 2713 ;**|**& #x 2713 ;**|
|[**Écriture LDAP**](active-directory-ds-comparison.md#ldap-write)|**& #x 2715 ;**|**& #x 2713 ;**|
|[**Stratégie de groupe**](active-directory-ds-comparison.md#group-policy)|Simple|Complète|
|[**Déploiements distribués geo**](active-directory-ds-comparison.md#geo-dispersed-deployments)|**& #x 2715 ;**|**& #x 2713 ;**|


#### <a name="managed-service"></a>Service géré
Domaines de Services de domaine Active Directory Azure sont gérés par Microsoft. Vous n’avez pas à vous soucier de correctifs, mises à jour, de contrôle, des sauvegardes et de garantir la disponibilité de votre domaine. Ces tâches de gestion sont proposés en tant que service par Microsoft Azure pour vos domaines gérées.

#### <a name="secure-deployments"></a>Sécuriser les déploiements
Le domaine géré est verrouillé en toute sécurité vers le bas en fonction meilleures pratiques de sécurité de Microsoft pour les déploiements AD. Les meilleures pratiques suivantes proviennent de chefs de l’équipe du produit AD d’expérience d’ingénierie et les déploiements AD de prise en charge. Pour les déploiements personnalisables, vous devez prendre des mesures de déploiement spécifique pour verrouiller la banque d’informations sécurisé vers le bas/votre déploiement.

#### <a name="dns-server"></a>Serveur DNS
Un domaine géré Azure Active Directory Domain Services comprend gérées services DNS. Les membres du groupe « Administrateurs de DC AAD » peuvent gérer DNS sur le domaine géré. Membres de ce groupe figurent des privilèges d’Administration DNS complets pour le domaine géré. Gestion du DNS peut être effectuée à l’aide de la « console d’Administration DNS « incluse dans le package serveur outils (administration distant).

#### <a name="domain-or-enterprise-administrator-privileges"></a>Privilèges d’administrateur de domaine ou entreprise
Ces privilèges ne sont pas proposées sur un domaine géré AAD DS. Applications nécessitant ces privilèges élevés pour être installé/exécuter ne peuvent pas être exécutées avec les domaines gérés. Un sous-ensemble des privilèges administrateur plus petit est disponible pour les membres du groupe d’administration déléguée appelée « AAD DC administrateurs ». Ces privilèges incluent des privilèges pour configurer le système DNS, configurez la stratégie de groupe, obtenir des privilèges d’administrateur sur les ordinateurs à un domaine etc..

#### <a name="domain-join"></a>Jonction de domaine
Vous pouvez rejoindre machines virtuelles pour le domaine géré similaire à comment joindre des ordinateurs à un domaine Active Directory.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Authentification de domaine à l’aide de NTLM et Kerberos
Avec les Services de domaine Active Directory Azure, vous pouvez utiliser vos informations d’identification d’entreprise pour vous authentifier avec le domaine géré. Informations d’identification sont synchronisées avec votre client Azure AD. Pour les clients synchronisés, Azure AD Connect garantit que les modifications apportées aux informations d’identification apportées locaux sont synchronisées avec Azure AD. Avec une configuration d’un domaine personnalisé, vous devrez peut-être configurer une relation d’approbation de domaine avec une forêt de comptes locaux pour les utilisateurs pour vous authentifier avec leurs informations d’identification d’entreprise. Vous pouvez également cliquer, vous devrez peut-être configurer la réplication Active Directory pour vous assurer que les mots de passe utilisateur synchronisent à vos machines virtuelles de contrôleur domaine Azure.

#### <a name="custom-ou-structure"></a>Structure d’unité d’organisation personnalisée
Membres du groupe « Administrateurs de DC AAD » peuvent créer des unités d’organisation personnalisées dans le domaine géré.

#### <a name="schema-extensions"></a>Extensions de schéma
Vous ne pouvez pas étendre le schéma de base d’un domaine géré Azure Active Directory Domain Services. Par conséquent, applications qui s’appuient sur les extensions de schéma Active Directory (par exemple, les nouveaux attributs sous l’objet utilisateur) ne peut pas être levées et décalé vers l’aux domaines AAD DS.

#### <a name="ad-domain-or-forest-trusts"></a>Domaine Active Directory ou les approbations de forêt
Domaines gérés ne peut pas être configurés pour définir des relations d’approbation (entrant/sortant) avec d’autres domaines. Par conséquent, scénarios tels que les déploiements forêt ressource ou si vous préférez ne pas être synchronisé Azure AD des mots de passe ne peut pas utiliser Azure Active Directory Domain Services.

#### <a name="ldap-read"></a>Lecture LDAP
Le domaine géré prend en charge les charges de travail de lecture LDAP. Par conséquent, vous pouvez déployer des applications qui effectuent des opérations de lecture LDAP sur le domaine géré.

#### <a name="secure-ldap"></a>LDAP sécurisé
Vous pouvez configurer Azure Active Directory Domain Services pour fournir un accès sécurisé LDAP à votre domaine géré, y compris via internet.

#### <a name="ldap-write"></a>Écriture LDAP
Le domaine géré est en lecture seule pour les objets utilisateur. Par conséquent, les applications qui effectuent des opérations d’écriture LDAP contre les attributs de l’objet utilisateur ne fonctionnent pas dans un domaine géré. En outre, les mots de passe utilisateur ne peut pas être modifiées dans le domaine géré. Un autre exemple serait modification d’appartenances ou les attributs d’un groupe dans le domaine géré, qui n’est pas autorisée. Toutefois, les modifications d’attributs de l’utilisateur ou des mots de passe dans Azure Active Directory (via le portail de PowerShell/Azure) ou locaux AD sont synchronisés avec le domaine géré AAD DS.

#### <a name="group-policy"></a>Stratégie de groupe
Constructions de stratégie de groupe complexes ne sont pas prises en charge sur le domaine géré AAD DS. Par exemple, vous ne peut pas créer et déployer des objets stratégie de groupe distinct pour chaque unité d’organisation personnalisée dans le domaine ou utilisez filtrage afin de cibler GP WMI. Il existe une intégrés stratégie de groupe chaque des conteneurs « AADDC utilisateurs » et « Ordinateurs AADDC », ce qui peut être personnalisée pour configurer la stratégie de groupe.

#### <a name="geo-dispersed-deployments"></a>Déploiements dispersion geo
Azure domaines gérés les Services de domaine Active Directory sont disponibles dans un seul réseau virtuel dans Azure. Pour les scénarios qui requièrent superflus soit disponible dans plusieurs Azure régions du monde entier, la définition un contrôleur de domaine dans Azure IaaS machines virtuelles peut être le meilleur choix.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>Options de déploiement (soi-même) AD « Personnalisables »
Vous devrez utiliser en cas de déploiement où vous devez certaines des fonctionnalités proposées par une installation Windows Server AD. Dans ce cas, vous pouvez une des options (soi-même) personnalisables suivantes :

- **Domaine cloud autonome :** Vous pouvez configurer une instance autonome 'domaine cloud' à l’aide de machines virtuelles Azure qui ont été configurés comme domaine. Cette infrastructure n’est pas intégré à vos localement environnement Active Directory. Cette option nécessite un ensemble différent de 'informations d’identification cloud' login/administrer machines virtuelles dans le cloud.

- **De déploiement de ressources forêt :** Vous pouvez configurer un domaine dans la topologie de forêt ressource, à l’aide de machines virtuelles Azure configurés comme domaine. Ensuite, vous pouvez configurer une relation d’approbation AD avec vos localement environnement Active Directory. Vous pouvez ordinateurs jonction de domaine (machines virtuelles Azure) à la forêt ressource dans le cloud. Authentification de l’utilisateur qui passe sur des options une connexion VPN/ExpressRoute à votre répertoire local.

- **Étendre votre domaine local vers Azure :** Vous pouvez connecter un réseau virtuel Azure à votre réseau local à l’aide d’une connexion VPN/ExpressRoute, afin que les ordinateurs virtuels Azure peuvent être joints à vos localement AD. Une autre solution consiste à promouvoir contrôleurs réplica de votre domaine local dans Azure comme une machine virtuelle. Vous pouvez ensuite configurez-la répliquer via une connexion VPN/ExpressRoute à votre répertoire local. Ce mode déploiement étend efficacement votre domaine local vers Azure.

> [AZURE.NOTE] Vous pouvez déterminer qu’une option utilitaire est mieux adaptée votre cas d’utilisation déploiement. Envisagez de [partager des commentaires](active-directory-ds-contact-us.md) pour nous aider à comprendre les fonctionnalités seraient vous permettent de vous avez choisi Azure Active Directory Domain Services à l’avenir. Ces commentaires nous permet d’évoluer le service pour l’adapter à vos besoins de déploiement et de cas d’utilisation.

Nous avons publié [aux instructions pour le déploiement d’Active Directory Windows Server Azure machines virtuelles en fonctionnement](https://msdn.microsoft.com/library/azure/jj156090.aspx) pour faciliter les installations utilitaire.


## <a name="related-content"></a>Contenu associé
- [Fonctionnalités - Azure Active Directory Domain Services](active-directory-ds-features.md)

- [Scénarios de déploiement - Services de domaine Active Directory Azure](active-directory-ds-scenarios.md)

- [Instructions pour le déploiement d’Active Directory Windows Server sur des Machines virtuelles Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
