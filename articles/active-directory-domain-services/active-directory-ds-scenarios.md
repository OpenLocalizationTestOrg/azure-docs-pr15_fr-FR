<properties
    pageTitle="Azure Active Directory Domain Services : Scénarios de déploiement | Microsoft Azure"
    description="Scénarios de déploiement d’Azure Active Directory Domain Services"
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
    ms.date="09/21/2016"
    ms.author="maheshu"/>


# <a name="deployment-scenarios-and-use-cases"></a>Scénarios de déploiement et d’exemples d’utilisation
Dans cette section, nous examinons quelques scénarios et utilisations bénéficient de Services de domaine Azure Active Directory (AD).

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Banque d’informations sécurisé, facilite l’administration des machines virtuelles Azure
Vous pouvez utiliser Azure Active Directory Domain Services pour gérer vos machines virtuelles Azure d’une manière simplifiée. Machines virtuelles Azure peuvent être joints au domaine géré, et donc afin de pouvoir utiliser vos informations d’identification Active Directory d’entreprise pour vous connecter. Cette approche permet d’éviter les soucis de gestion des informations d’identification telles que la gestion des comptes d’administrateur local sur chacun de vos machines virtuelles Azure.

Machines virtuelles Server qui sont jointes au domaine géré peut également être gérés et sécurisés à l’aide de la stratégie de groupe. Vous pouvez appliquer des bases de sécurité nécessaires à vos machines virtuelles Azure et les verrouiller conformément aux directives de sécurité de l’entreprise. Par exemple, vous pouvez utiliser les fonctionnalités de gestion de stratégie de groupe pour restreindre les types d’applications qui peuvent être lancées sur ces machines virtuelles.

![Rationaliser l’administration des machines virtuelles Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Que serveurs et autres infrastructure atteint la fin de vie, Contoso se déplace de nombreuses applications actuellement hébergées en local dans le cloud. Leur standard informatiques en cours impose que les serveurs hébergeant les applications d’entreprise doivent être gérés à l’aide de la stratégie de groupe et à un domaine. Contoso administrateur informatique préfère machines virtuelles de domaine jointure déployées dans Azure, pour simplifier l’administration. Par conséquent, administrateurs et utilisateurs peuvent se connecter à l’aide de leurs informations d’identification d’entreprise. En même temps, machines peuvent être configurés pour être conformes aux bases de sécurité nécessaires à l’aide de la stratégie de groupe. Vous préférez ne pas devoir déployer, surveiller et gérer les contrôleurs de domaine dans Azure pour sécuriser Azure machines virtuelles contoso. Par conséquent, les Services de domaine Active Directory Azure est parfait pour ce cas d’utilisation.

**Remarques sur le déploiement**

Tenez compte des points importants suivants pour ce scénario de déploiement :

- Domaines gérés fournies par les Services de domaine Active Directory Azure fournissent une structure unité d’organisation (unité d’organisation) plate unique par défaut. Tous les ordinateurs à un domaine résident dans une seule unité d’organisation plate. Vous pouvez toutefois choisir de créer des unités d’organisation personnalisées.

- Azure Active Directory Domain Services prend en charge la stratégie de groupe simple sous la forme d’un intégrés stratégie de groupe chaque pour les utilisateurs et les ordinateurs conteneurs. Vous ne pouvez pas cibler GP par département/unité d’organisation, effectuer un filtrage WMI ou créer stratégie de groupe personnalisé.

- Les Services de domaine Active Directory Azure prend en charge le schéma d’objet base AD ordinateur. Vous ne pouvez pas étendre schéma de l’objet ordinateur.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Levée et MAJ une application locale qui utilise l’authentification de liaison LDAP Azure Infrastructure Services

![Liaison LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso possède une application locale qui a été achetée à partir d’un fil nombreuses années antérieures. L’application est actuellement en mode maintenance le fournisseur de logiciels et les modifications apportées à l’application de la demande sont trop coûteuses de Contoso. Cette application possède un site Web frontal web qui recueille des informations d’identification utilisateur à l’aide d’un formulaire web et puis authentifie les utilisateurs en effectuant une liaison LDAP à l’annuaire Active Directory d’entreprise. Contoso souhaite migrer Azure Infrastructure Services de cette application. Il est préférable que l’application fonctionne comme, sans avoir aux modifier. En outre, les utilisateurs doivent pouvoir s’authentifier à l’aide de leurs informations d’identification d’entreprise existantes et sans avoir à former les utilisateurs à effectuer les actions différemment. En d’autres termes, les utilisateurs finaux doit être quelle que soit de l’endroit où l’application est en cours d’exécution et la migration doit être transparente pour eux.

**Remarques sur le déploiement**

Tenez compte des points importants suivants pour ce scénario de déploiement :

- Assurez-vous que l’application n’a pas besoin modification/écriture à l’annuaire. Accès en écriture LDAP aux domaines gérés fournies par les Services de domaine Active Directory Azure n’est pas pris en charge.

- Vous ne pouvez pas modifier les mots de passe directement sur le domaine géré. Utilisateurs finaux peuvent modifier leur mot de passe soit à l’aide de mécanisme de modification du mot de passe libre-service d’Azure AD ou par rapport au répertoire local. Ces modifications sont automatiquement synchronisés et disponibles dans le domaine géré.


## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Décalage levée et une application locale qui utilise le protocole LDAP lecture pour accéder au répertoire Azure Infrastructure Services
Contoso a une application de ligne-de-métier locale qui a été développée presque dix ans auparavant. Cette application est répertoire prenant en charge et a été conçue pour fonctionner avec Windows Server AD. L’application utilise LDAP (Lightweight Directory Access Protocol) pour lire les attributs/des informations relatives aux utilisateurs d’Active Directory. L’application ne modifier les attributs ni dans le cas contraire écrire dans l’annuaire. Contoso souhaite migrer Azure Infrastructure Services de cette application et en retirer le matériel de locaux vieillissants actuellement hébergeant cette application. L’application ne peut pas être réécrits pour utiliser le répertoire moderne API telles que l’API Azure AD Graph basée sur REST. Par conséquent, une option de levée et MAJ est souhaitée selon laquelle elle peut être migrée pour exécuter dans le cloud, sans modifier le code ou réécriture de l’application.

**Remarques sur le déploiement**

Tenez compte des points importants suivants pour ce scénario de déploiement :

- Assurez-vous que l’application n’a pas besoin modification/écriture à l’annuaire. Accès en écriture LDAP aux domaines gérés fournies par les Services de domaine Active Directory Azure n’est pas pris en charge.

- Assurez-vous que l’application n’a pas besoin un schéma Active Directory étendu/personnalisé. Extensions de schéma ne sont pas prises en charge dans Azure Active Directory Domain Services.


## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migrer une application de service ou processus local vers Azure Infrastructure Services
Certaines applications se composent de plusieurs niveaux, où un des niveaux doit effectuer des appels authentifiés vers un niveau principal comme un niveau de base de données. Comptes de service Active Directory sont utilisés dans ces exemples d’utilisation. Vous pouvez levée et MAJ ces applications dans Azure Infrastructure Services et utilisez les Services de domaine Active Directory Azure pour les besoins de l’identité de ces applications. Vous pouvez choisir d’utiliser le même compte de service est synchronisé à partir de votre annuaire local vers Azure Active Directory. Vous pouvez également cliquer, vous pouvez tout d’abord créer une unité d’organisation personnalisée et puis créer un compte de service distinct dans cette unité d’organisation pour déployer des applications.

![Compte de service à l’aide de WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso possède une application de l’archivage sécurisé logicielle personnalisée qui inclut un site web frontal, un serveur SQL server et un serveur principal FTP. L’authentification Windows intégrée des comptes de service est utilisée pour authentifier le site web frontal au serveur FTP. Le site web frontal est configuré pour exécuter en tant qu’un compte de service. Le serveur principal est configuré pour autoriser l’accès à partir du compte de service pour le site web frontal. Contoso préfère ne pas d’avoir à déployer un ordinateur virtuel de contrôleur de domaine dans le cloud pour déplacer Azure Infrastructure Services de cette application. Contoso administrateur peut déployer les serveurs hébergeant le site web frontal, SQL server et le serveur FTP aux machines virtuelles Azure. Ces ordinateurs sont puis joints à un domaine géré Azure Active Directory Domain Services. Puis, ils peuvent utilisent le même compte de service dans leur répertoire local pour l’authentification de l’application. Ce compte de service est synchronisé avec le domaine géré Azure Active Directory Domain Services et peut être utilisé.

**Remarques sur le déploiement**

Tenez compte des points importants suivants pour ce scénario de déploiement :

- Assurez-vous que l’application utilise le nom d’utilisateur et mot de passe pour l’authentification. Certificat/carte à puce en fonction de l’authentification n’est pas pris en charge par les Services de domaine Active Directory Azure.

- Vous ne pouvez pas modifier les mots de passe directement sur le domaine géré. Utilisateurs finaux peuvent modifier leur mot de passe soit à l’aide de mécanisme de modification du mot de passe libre-service d’Azure AD ou par rapport au répertoire local. Ces modifications sont automatiquement synchronisés et disponibles dans le domaine géré.


## <a name="azure-remoteapp"></a>RemoteApp Azure
Azure RemoteApp permet à l’administrateur de Contoso créer une collection de sites à un domaine. Cette fonctionnalité permet aux applications à distance pris en charge par Azure RemoteApp à s’exécuter sur des ordinateurs à un domaine et accéder à d’autres ressources à l’aide de l’authentification Windows intégrée. Contoso peut utiliser les Services de domaine Active Directory Azure à fournir un domaine géré utilisé par les collections Azure RemoteApp à un domaine.

![RemoteApp Azure](./media/active-directory-domain-services-scenarios/azure-remoteapp.png)

Pour plus d’informations sur ce scénario de déploiement, voir l’article de Blog de Services Bureau à distance intitulé [levée et MAJ vos charges de travail avec Azure RemoteApp et Azure Active Directory Domain Services](http://blogs.msdn.com/b/rds/archive/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services.aspx).
