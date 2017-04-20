<properties 
    pageTitle="Les méthodes conseillées pour l’utilisation de l’authentification Multifacteur Azure"
    description="Ce document fournit les meilleures pratiques concernant l’utilisation de l’authentification Multifacteur Azure avec comptes Azure"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Les méthodes conseillées pour l’utilisation de l’authentification multifacteur Azure avec les comptes Azure Active Directory

Lorsqu’il s’agit à l’amélioration de votre processus d’authentification, l’authentification multifacteur est le choix par défaut pour la plupart des organisations. Azure multifacteur l’authentification Multifacteur permet aux entreprises de répondre aux exigences de sécurité et conformité tout en fournissant une expérience de connexion simple pour leurs utilisateurs. Cet article traite des pratiques recommandées que vous devez prendre en compte lors de la planification pour l’adoption de l’authentification Multifacteur Azure.

## <a name="best-practices-for-azure-multi-factor-authentication-in-the-cloud"></a>Meilleures pratiques pour l’authentification multifacteur Azure dans le cloud
Afin de fournir l’ensemble de vos utilisateurs avec l’authentification multifacteur et tirer parti des riches fonctionnalités que l’authentification multifacteur Azure offre, vous devez activer l’authentification multifacteur Azure sur l’ensemble de vos utilisateurs.  Cela revient à l’aide d’une des opérations suivantes :

- Azure AD Premium ou la gamme de mobilité d’entreprise
- Fournisseur d’authentification multifacteur

### <a name="azure-ad-premiumenterprise-mobility-suite"></a>Azure AD Premium/entreprise mobilité Suite

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

La première étape recommandée pour adopter l’authentification Multifacteur Azure dans le cloud à l’aide de Azure AD Premium ou la Suite de mobilité Enterprise consiste à attribuer des licences à vos utilisateurs.  L’authentification multifacteur Azure fait partie de ces suites et en tant que tel votre organisation ne doit pas rien supplémentaires pour étendre la fonctionnalité de l’authentification multifacteur à tous les utilisateurs.

Lorsque la configuration de l’authentification multifacteur prendre en considération suivants :

- Vous n’avez pas besoin créer un fournisseur d’authentification multifacteur.  Azure AD Premium et la Suite de mobilité Enterprise est fourni avec l’authentification multifacteur Azure.  Si vous créez un fournisseur Auth vous pouvez obtenir double facturés.
- Azure AD Connect n’est nécessaire si vous synchronisez votre environnement Active Directory local avec un annuaire Azure AD. Si vous utilisez uniquement un répertoire Azure AD qui n’est pas synchronisé avec une instance locale d’Active Directory, vous n’avez pas besoin d’Azure AD Connect.


### <a name="multi-factor-auth-provider"></a>Fournisseur d’authentification multifacteur

![Fournisseur d’authentification multifacteur](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Si vous n’avez pas Azure AD Premium ou la Suite de mobilité Enterprise, la première étape recommandée pour adopter l’authentification Multifacteur Azure dans le nuage est pour créer un fournisseur Auth l’authentification Multifacteur. Bien que l’authentification Multifacteur est disponible par défaut pour les administrateurs globaux qui ont Azure Active Directory, lorsque vous déployez l’authentification Multifacteur pour votre organisation, vous devez étendre la fonctionnalité de l’authentification multifacteur à tous les utilisateurs et faire que vous avez besoin d’un fournisseur d’authentification multifacteur.
Lorsque vous sélectionnez le fournisseur Auth, vous devez sélectionner un répertoire et posez-vous les questions suivantes :

- Vous n’avez pas besoin d’un répertoire Azure AD pour créer un fournisseur d’authentification multifacteur.
- Vous devrez associer un répertoire Azure AD le fournisseur d’authentification multifacteur si vous souhaitez étendre l’authentification multifacteur à l’ensemble de vos utilisateurs et/ou souhaite pas que votre administrateur global pour pouvoir tirer parti des fonctionnalités telles que le portail de gestion, le message d’accueil personnalisé et des rapports.
- Synchronisation d’annuaire ou AAD Sync sont uniquement une obligation si vous synchronisez votre environnement Active Directory local avec un annuaire Azure AD. Si vous utilisez uniquement un répertoire Azure AD qui n’est pas synchronisé avec une instance locale d’Active Directory, vous n’avez pas besoin DirSync ou AAD Sync.
- Si vous avez Azure AD Premium ou la gamme de mobilité d’entreprise, vous n’avez pas besoin créer un fournisseur d’authentification multifacteur. Vous ne devez attribuer une licence à un utilisateur et commencer à activation de l’authentification Multifacteur pour les utilisateurs.
- Assurez-vous d’avoir choisi le modèle d’utilisation appropriée pour votre entreprise (par auth ou par utilisateur activé), une fois que vous sélectionnez le modèle d’utilisation que vous ne pouvez pas le modifier.

### <a name="user-account"></a>Compte d’utilisateur
Lorsque vous activez l’authentification Multifacteur pour vos utilisateurs, les comptes d’utilisateurs peuvent être l’un des trois états principaux : désactivé, activé ou appliquée.
Suivez les instructions ci-dessous pour vous assurer que vous utilisez l’option la plus appropriée pour votre déploiement :

- Lorsque l’état est défini sur désactivé, cet utilisateur n’utilise pas l’authentification multifacteur. Il s’agit de l’état par défaut.
- Lorsque l’état est défini sur activé, cela signifie que l’utilisateur est activé, mais n’a pas terminé le processus d’inscription. Il devra pour terminer le processus à la connexion suivante. Ce paramètre n’affecte pas les applications de navigateur non. Toutes les applications continueront à fonctionner jusqu'à ce que le processus d’inscription est terminé.
- Lorsque l’état est défini sur appliquée, cela signifie que l’utilisateur peut ou ne peut-être pas encore terminé l’enregistrement. Si ils ont terminé le processus d’inscription qu’ils utilisent l’authentification multifacteur. Dans le cas contraire, l’utilisateur sera invité à terminer le processus d’inscription à la connexion suivante. Ce paramètre n’affecte pas les applications de navigateur non. Ces applications ne fonctionneront pas jusqu'à ce que les mots de passe application sont créées et utilisées.
- Utiliser le modèle de Notification utilisateur disponibles dans l’article [mise en route avec l’authentification multifacteur Azure dans le cloud](multi-factor-authentication-get-started-cloud.md) pour envoyer un message électronique à vos utilisateurs en ce qui concerne l’adoption de l’authentification Multifacteur.

### <a name="supportability"></a>Prise en charge

Étant donné que la plupart des utilisateurs êtes habitué à utiliser uniquement les mots de passe pour l’authentification, il est important que votre société mettre sensibilisation à tous les utilisateurs relative à cette procédure. Cette conscience pouvez encore réduire la probabilité que les utilisateurs appellent votre support technique pour les questions secondaires liées à l’authentification Multifacteur.
Toutefois, il existe certains scénarios dans lesquels il est nécessaire de désactiver temporairement l’authentification Multifacteur. Suivez les instructions ci-dessous pour comprendre comment gérer ces scénarios :

- Vérifiez que votre équipe de support technique est formé à l’endroit où l’application mobile ou téléphone pas reçoit une notification ou un appel téléphonique des scénarios poignée et pour cette raison que l’utilisateur ne peut pas se connecter. Ils peuvent activer une option unique autoriser un utilisateur s’authentifier une seule fois par « contournement » de l’authentification multifacteur. La dérivation est temporaire et expire après le nombre de secondes spécifié.
- Si nécessaire, vous pouvez utiliser la fonctionnalité d’adresses IP approuvés dans l’authentification Multifacteur Azure. Cette fonctionnalité permet aux administrateurs d’un client géré ou fédéré la possibilité d’ignorer l’authentification multifacteur pour les utilisateurs sont se connecter à partir de l’intranet local de la société. Les fonctionnalités sont disponibles pour les clients Azure AD qui ont des licences Azure AD Premium, entreprise mobilité Suite ou l’authentification multifacteur Azure.


## <a name="best-practices-for-azure-multi-factor-authentication-on-premises"></a>Meilleures pratiques pour l’authentification multifacteur Azure local
Si votre entreprise a décidé de tirer parti de son propre infrastructure pour activer l’authentification Multifacteur, il sera nécessaire déployer un serveur de l’authentification multifacteur Azure en local. Les composants du serveur de l’authentification Multifacteur sont présentés dans l’illustration ci-dessous :

![Fournisseur d’authentification multifacteur](./media/multi-factor-authentication-security-best-practices/server.png)
*pas installé par défaut ** installé mais non activé par défaut


Serveur de l’authentification multifacteur Azure peuvent servir pour sécuriser les ressources de cloud et des ressources locales sont accessibles par les comptes Azure Active Directory.  Toutefois, cela peut seulement effectuée à l’aide de la fédération.  En d’autres termes, vous devez avoir AD FS et qu’il fédérée avec votre client Azure AD.
Lorsque la configuration serveur d’authentification multifacteur prendre en considération suivants :

- Si vous êtes sécurisation des ressources Azure AD à l’aide d’Active Directory Federation Services, puis le 1er facteur d’authentification est exécutée en local via AD FS et le facteur 2ème est exécutée en local par en respectant la demande.
- Il n’est pas obligatoire que le serveur de l’authentification multifacteur Azure être installé sur votre serveur de fédération AD FS alors que la carte de l’authentification multifacteur pour AD FS doit être installée sur un Windows Server 2012 R2 en cours d’exécution AD FS. Vous pouvez installer le serveur sur un autre ordinateur, dans la mesure où il s’agit d’une version prise en charge et installez la carte AD FS séparément sur votre serveur de fédération AD FS. Consultez la procédure ci-dessous pour obtenir des instructions sur l’installation de la carte séparément.
- L’Assistant installation de l’authentification multifacteur AD FS carte crée un groupe de sécurité appelé PhoneFactor Admins dans Active Directory, puis ajoute le compte du service de votre service de fédération AD FS à ce groupe. Il est recommandé de vérifier sur votre contrôleur de domaine que le groupe PhoneFactor Admins est bien créé et que le compte de service les AD FS est un membre de ce groupe. Si nécessaire, ajoutez manuellement le compte de service AD FS au groupe PhoneFactor Admins sur votre contrôleur de domaine.

### <a name="user-portal"></a>Portail de l’utilisateur
Ce portail s’exécute dans un site web Internet Information Server (IIS), qui permet aux fonctionnalités de libre-service et propose un ensemble complet de fonctionnalités administration utilisateur. Suivez les instructions ci-dessous pour configurer ce composant :

- IIS 6 ou supérieur est requis
- V2.0.507207 ASP.NET doivent être installés et enregistrés
- Ce serveur peut être déployé dans un réseau de périmètre.



### <a name="app-passwords"></a>Application de mots de passe
Si votre organisation est fédérée à l’aide de l’authentification unique avec Azure Active Directory et que vous vous apprêtez à utiliser l’authentification Multifacteur Azure, puis tenir compte des éléments suivants lors de l’utilisation de mots de passe de l’application (n’oubliez pas que cela s’applique uniquement aux fédéré (SSO) est utilisé) :

- Le mot de passe d’application vérifié par Azure Active Directory et par conséquent ignore la fédération. Fédération est utilisée uniquement lorsque vous configurez l’application de mot de passe.
- Fédéré (SSO) les mots de passe des utilisateurs sont stockées dans l’id d’organisation. Si l’utilisateur quitte l’entreprise, ces informations doit se déplace vers id d’organisation à l’aide de la synchronisation d’annuaire en temps réel. Désactiver/suppression du compte peut prendre jusqu'à 3 heures pour synchroniser, retarder désactiver/suppression du mot de passe de l’application dans Azure Active Directory.
- Paramètres de contrôle d’accès Client local ne sont pas respectés par mot de passe de l’application
- Aucune authentification locaux journalisation / capacité d’audit n’est disponible pour votre mot de passe application
- Plus éducation de l’utilisateur final est requise pour le client Microsoft Lync 2013.
- Certains modèles architecturales avancées peuvent nécessiter l’utilisation d’une combinaison de nom d’utilisateur d’organisation et les mots de passe et les mots de passe application lors de l’utilisation de l’authentification multifacteur avec des clients, selon l’endroit où ils s’authentifier. Pour les clients qui s’authentifier une infrastructure en local, vous utiliserez une organisation nom d’utilisateur et mot de passe. Pour les clients qui s’authentifier Azure AD, vous devez utiliser le mot de passe d’application.
- Par défaut, les utilisateurs ne peuvent pas créer des mots de passe de l’application, si votre société requiert qu’ou si vous avez besoin permettre aux utilisateurs de créer un mot de passe de l’application dans certains scénarios, vérifiez que les utilisateurs d’autoriser option à créer l’application de mots de passe pour vous connecter à des applications de navigateur non est sélectionné.

## <a name="additional-considerations"></a>Considérations supplémentaires
Utilisez la liste ci-dessous pour comprendre quelques considérations supplémentaires et des pratiques recommandées pour chaque composant qui sera déploiement en local :

Méthode|Description
:------------- | :------------- |
[Service de fédération Active Directory](multi-factor-authentication-get-started-adfs.md)|Informations sur la configuration de l’authentification multifacteur Azure avec AD FS.
[Authentification RADIUS](multi-factor-authentication-get-started-server-radius.md)|  Informations sur le programme d’installation et configuration du serveur de l’authentification Multifacteur Azure avec RADIUS.
[Authentification IIS](multi-factor-authentication-get-started-server-iis.md)|Informations sur le programme d’installation et configuration du serveur de l’authentification Multifacteur Azure avec IIS.
[Authentification Windows](multi-factor-authentication-get-started-server-windows.md)|  Informations sur le programme d’installation et configuration du serveur de l’authentification Multifacteur Azure avec l’authentification Windows.
[Authentification LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informations sur le programme d’installation et configuration du serveur de l’authentification Multifacteur Azure avec l’authentification LDAP.
[À distance de la passerelle de bureau et de serveur de l’authentification multifacteur Azure à l’aide de RADIUS](multi-factor-authentication-get-started-server-rdg.md)|  Informations sur le programme d’installation et configuration du serveur de l’authentification Multifacteur Azure avec passerelle Bureau à distance à l’aide de RADIUS.
[Synchronisation avec Active Directory Windows Server](multi-factor-authentication-get-started-server-dirint.md)|Informations sur le programme d’installation et configuration de la synchronisation entre Active Directory et le serveur de l’authentification Multifacteur Azure.
[Déploiement du Service d’authentification multifacteur Azure serveur Web App Mobile](multi-factor-authentication-get-started-server-webservice.md)|Informations sur le programme d’installation et la configuration du service web de serveur de l’authentification Multifacteur Azure.
[Configuration avancée VPN avec l’authentification multifacteur Azure](multi-factor-authentication-advanced-vpn-configurations.md)|Informations sur la configuration des appareils Juniper/Pulse Secure VPN Cisco ASA et Citrix Netscaler à l’aide de LDAP ou RADIUS.


## <a name="additional-resources"></a>Ressources supplémentaires
Si cet article présente quelques méthodes conseillées pour l’authentification Multifacteur Azure, il existe d’autres ressources que vous pouvez également utiliser lors de la planification de votre déploiement de l’authentification Multifacteur. La liste ci-dessous comporte quelques articles clés qui peuvent vous aider lors de ce processus :

- [Rapports dans l’authentification multifacteur Azure](multi-factor-authentication-manage-reports.md)
- [Mise en route pour l’authentification multifacteur Azure](multi-factor-authentication-end-user-first-time.md)
- [Forum aux questions sur l’authentification multifacteur Azure](multi-factor-authentication-faq.md)
