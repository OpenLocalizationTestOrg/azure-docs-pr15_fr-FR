<properties
    pageTitle="Guide de sécurité de stockage Azure | Microsoft Azure"
    description="Détails de nombreuses méthodes de sécurisation du stockage Azure, y compris mais non limité à RBAC, chiffrement de Service de stockage, le chiffrement côté Client, PME 3.0 et le chiffrement des disques Azure."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="robinsh"/>

#<a name="azure-storage-security-guide"></a>Guide de sécurité de stockage Azure

##<a name="overview"></a>Vue d’ensemble

Stockage Azure propose un ensemble complet de fonctionnalités de sécurité qui ensemble permettent aux développeurs de créer des applications sécurisées. Le compte de stockage peut être sécurisé à l’aide de contrôle d’accès basé sur un rôle et Azure Active Directory. Les données peuvent être sécurisées lors des transferts entre une application et Azure en utilisant [Le chiffrement côté Client](storage-client-side-encryption.md), HTTPS ou PME 3.0. Données peuvent être définies à chiffrer automatiquement lors de l’écriture au stockage Azure à l’aide de [Chiffrement de Service de stockage (SSE)](storage-service-encryption.md). Système d’exploitation et données disques utilisés par les machines virtuelles peuvent être définies pour être chiffrés à l’aide de [Chiffrement des disques Azure](../security/azure-security-disk-encryption.md). Accès délégué aux objets de données dans le stockage Azure peuvent être accordées à l’aide de [Signatures d’accès partagé](storage-dotnet-shared-access-signature-part-1.md).

Cet article offre un aperçu de chacune de ces fonctionnalités de sécurité qui peuvent être utilisées avec le stockage Azure. Des liens sont fournis vers des articles qui donnent détails de chaque fonctionnalité, vous pouvez facilement effectuer une recherche sur chaque sujet plus approfondie.

Voici les rubriques qui seront traités dans cet article :

-   [Sécurité du plan d’administration](#management-plane-security) – sécurisation de votre compte de stockage

    Le plan de gestion des se compose des ressources utilisées pour gérer votre compte de stockage. Dans cette section, nous aborderons le modèle de déploiement d’Azure le Gestionnaire de ressources et comment utiliser le contrôle d’accès basé sur un rôle (RBAC) pour contrôler l’accès à vos comptes de stockage. Nous allons aborder également gérer vos clés de compte de stockage et comment les régénérer.

-   [Données plan sécurité](#data-plane-security) – sécurisation de l’accès à vos données

    Dans cette section, nous allons aborder permettant d’accéder aux objets de données réelles dans votre compte de stockage, tels que des objets BLOB, les fichiers, les files d’attente et les tables, à l’aide de partagés accès Signatures et stockées les stratégies d’accès. Cet article aborde les associations de sécurité au niveau de service et les associations de sécurité au niveau des comptes. Nous verrons également comment limiter l’accès à une adresse IP spécifique (ou plage d’adresses IP), comment vous pouvez limiter le protocole utilisé pour HTTPS et comment révoquer une Signature accès partagé sans attendre sa date d’expiration.

-   [Chiffrement lors des transferts](#encryption-in-transit)

    Cette section explique comment faire pour sécuriser les données lorsque vous le transférez ou disparition stockage Azure. Nous aborderons ensuite l’utilisation de HTTPS et le chiffrement utilisé par PME 3.0 Azure partages de fichiers recommandée. Nous allons également au chiffrement côté Client, qui permet de chiffrer les données avant leur transfert dans le stockage dans une application cliente et déchiffrer les données après transfert se déconnecter de stockage.

-   [Chiffrement inactives](#encryption-at-rest)

    Nous allons aborder chiffrement de Service de stockage (SSE), et comment vous pouvez activer pour un compte de stockage, ce qui crée de vos objets BLOB bloc, des objets BLOB de la page et ajoutez des objets BLOB en cours de chiffrement automatiquement lors de l’écriture au stockage Azure. Nous allons étudier également comment vous pouvez utiliser le chiffrement des disques Azure et découvrez les différences de base et les cas de chiffrement de disque et SSE et chiffrement côté Client. Nous allons étudier brièvement la conformité FIPS pour les ordinateurs gouvernement des États-Unis.

-   À l’aide de [Stockage Analytique](#storage-analytics) à un audit de l’accès au stockage Azure

    Cette section explique comment trouver des informations dans les journaux d’analytique de stockage pour une demande. Nous examinons analytique stockage réel données du journal et découvrez comment déterminer si une demande est effectuée avec la clé de compte de stockage, avec une signature accès partagé, ou de manière anonyme et si elle a réussi ou a échoué.

-   [Activation des Clients via un navigateur à l’aide de CORS](#Cross-Origin-Resource-Sharing-CORS)

    Cette section parle comment autoriser (CORS) le partage des ressources origine croisée. Nous aborderons ensuite accès aux autres domaines et comment gérer avec les capacités CORS intégrées à stockage Azure.

##<a name="management-plane-security"></a>Sécurité de plan de gestion

Le plan de gestion des se compose opérations incidence sur le compte de stockage. Par exemple, vous pouvez créer ou supprimer un compte de stockage, obtenir une liste des comptes de stockage dans un abonnement, récupérer les clés de compte de stockage ou régénérer les clés de compte de stockage.

Lorsque vous créez un nouveau compte de stockage, vous sélectionnez un modèle de déploiement de classique ou le Gestionnaire de ressources. Le modèle classique de création de ressources dans Azure autorise uniquement l’accès tout ou rien à l’abonnement et à son tour, le compte de stockage.

Ce guide se concentre sur le modèle de gestionnaire de ressources qui est le moyen recommandé pour la création de comptes de stockage. Avec les comptes de stockage Gestionnaire de ressources, plutôt que donnant accès à tout l’abonnement, vous pouvez contrôler l’accès à un niveau plus déterminée au plan de gestion à l’aide de contrôle d’accès basé sur un rôle (RBAC).

###<a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>La sécurisation de votre compte de stockage avec contrôle d’accès basé sur un rôle (RBAC)

Parlons qu’est RBAC, et comment vous pouvez l’utiliser. Chaque abonnement Azure a un Azure Active Directory. Les utilisateurs, des groupes et des applications à partir de ce répertoire peuvent être autorisées à accéder pour gérer les ressources de l’abonnement Azure qui utilisent le modèle de déploiement du Gestionnaire de ressources. Ceci est appelé contrôle d’accès basé sur un rôle (RBAC). Pour gérer cette access, vous pouvez utiliser le [portail Azure](https://portal.azure.com/), les [Outils Azure infrastructure du langage commun](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)ou l' [API REST de Azure Storage ressource fournisseur](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Avec le modèle de gestionnaire de ressources, placez le compte de stockage dans un groupe et contrôler l’accès ressource pour le plan de gestion de ce compte de stockage spécifique à l’aide d’Azure Active Directory. Par exemple, vous pouvez donner à des utilisateurs spécifiques la fonctionnalité permettant d’accéder aux clés de compte de stockage, tandis que d’autres utilisateurs peuvent afficher des informations sur le compte de stockage, mais ne peuvent pas accéder aux clés de compte de stockage.

####<a name="granting-access"></a>Pour accorder l’accès

L’accès en attribuant le rôle RBAC approprié aux utilisateurs, des groupes et des applications, au niveau de l’étendue droite. Pour accorder l’accès à tout l’abonnement, vous attribuez un rôle au niveau de l’abonnement. Vous pouvez accorder l’accès à toutes les ressources dans un groupe de ressources par octroyer des autorisations au groupe de ressources. Vous pouvez également attribuer des rôles spécifiques à des ressources spécifiques, tels que les comptes de stockage.

Voici les principaux points que vous devez savoir sur l’utilisation de RBAC pour accéder aux opérations de gestion d’un compte de stockage Azure :

-   Lorsque vous affectez access, vous affectez en fait un rôle au compte que vous voulez accéder. Vous pouvez contrôler l’accès à ces opérations permet de gérer ce compte de stockage, mais pas pour les objets de données dans le compte. Par exemple, vous pouvez accorder une autorisation pour récupérer les propriétés du compte de stockage (par exemple, redondance), mais pas à un conteneur ou des données dans un conteneur à l’intérieur de stockage d’objets Blob.

-   Pour une personne être autorisées à accéder aux objets de données dans le compte de stockage, vous pouvez autoriser à lire les clés de compte de stockage et cet utilisateur peut ensuite utiliser ces touches pour accéder aux objets BLOB, des files d’attente, des tableaux et des fichiers.

-   Rôles peuvent être affectées à un compte d’utilisateur spécifique, un groupe d’utilisateurs, ou pour une application spécifique.

-   Chaque rôle possède une liste des Actions et pas. Par exemple, le rôle de collaborateur Machine virtuelle comporte une Action de « listKeys » qui permet des clés de compte de stockage à lire. La collaboration comporte « Pas Actions » par exemple, la mise à jour le niveau d’accès pour les utilisateurs dans l’annuaire Active Directory.

-   Rôles pour le stockage inclure (mais ne sont pas limités à) ce qui suit :

    -   Propriétaire –, ils peuvent gérer tous les éléments, y compris l’accès.

    -   Collaboration – qu’il peut faire quoi que ce soit le propriétaire peut à l’exception des accorder un accès. Une personne disposant de ce rôle peut afficher et régénérer les clés de compte de stockage. Avec les touches de compte de stockage, ils peuvent accéder les objets de données.

    -   Lecteur – ils peuvent afficher les informations du compte de stockage, à l’exception des secrets. Par exemple, si vous affectez un rôle avec des autorisations de lecture sur le compte de stockage à une personne, ils peuvent afficher les propriétés du compte de stockage, mais ils ne peuvent pas apporter des modifications aux propriétés ou afficher les touches de compte de stockage.

    -   Collaboration de compte de stockage – qu’ils peuvent gérer le compte de stockage – qu’ils peuvent lire les groupes de ressources et des ressources, l’abonnement et créer et gérer les déploiements de groupe de ressources abonnement. Ils peuvent également accéder les clés de compte de stockage, qui à son tour signifie qu’ils peuvent accéder au plan de données.

    -   Administrateur de l’accès utilisateur : ils peuvent gérer l’accès utilisateur pour le compte de stockage. Par exemple, ils peuvent accorder l’accès en lecture à un utilisateur spécifique.

    -   Machine virtuelle collaboration – ils peuvent gérer des machines virtuelles, mais pas le compte de stockage auxquels ils sont connectés. Ce rôle peut répertorient les touches de compte de stockage, ce qui signifie que l’utilisateur auquel vous attribuez ce rôle peut mettre à jour le plan de données.

        Dans l’ordre d’un utilisateur créer une machine virtuelle, ils doivent être en mesure de créer le fichier de disque dur virtuel correspondant dans un compte de stockage. Pour ce faire, ils doivent être en mesure de récupérer la clé de compte de stockage et passer à l’API de création de la machine virtuelle. Par conséquent, ils doivent avoir cette autorisation afin qu’ils peuvent répertorier les clés de compte de stockage.

- La possibilité de définir des rôles personnalisés est une fonctionnalité qui permet de vous permet de créer un ensemble d’actions dans une liste d’actions disponibles qui peuvent être effectuées sur les ressources Azure.

- L’utilisateur doit être configuré dans votre Azure Active Directory avant que vous pouvez attribuer un rôle à leur.

- Vous pouvez créer un rapport de qui accordées/révoqué quel type d’accès vers ou à partir qui et dans quel étendue à l’aide de PowerShell ou l’infrastructure du langage commun Azure.

####<a name="resources"></a>Ressources

-   [Contrôle d’accès basé sur un rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md)

    Cet article explique le contrôle d’accès basé sur Azure Active Directory rôle et son fonctionnement.

-   [RBAC : Intégré rôles](../active-directory/role-based-access-built-in-roles.md)

    Cet article décrit tous les rôles intégrés disponibles dans la section RBAC.

-   [Présentation de déploiement du Gestionnaire de ressources et déploiement classique](../resource-manager-deployment-model.md)

    Cet article décrit le déploiement du Gestionnaire de ressources et les modèles de déploiement classique et explique les avantages de l’utilisation de groupes Gestionnaire de ressources et des ressources

-   [Cluster Azure réseau et des fournisseurs de stockage sous le Gestionnaire de ressources Azure](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

    Cet article explique comment calculer Azure, réseau et fournisseurs de stockage fonctionnent sous le modèle de gestionnaire de ressources.

-   [Gestion de contrôle d’accès basé sur un rôle avec l’API REST](../active-directory/role-based-access-control-manage-access-rest.md)

    Cet article vous explique comment utiliser l’API REST pour gérer RBAC.

-   [Référence des API REST stockage Azure ressource fournisseur](https://msdn.microsoft.com/library/azure/mt163683.aspx)

    Il s’agit de la référence de l’API que vous pouvez utiliser pour gérer votre compte de stockage par programme.

-   [Guide du développeur pour auth avec l’API Azure Gestionnaire de ressources](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

    Cet article vous explique comment authentifier à l’aide de l’API Gestionnaire de ressources.

-   [Contrôle d’accès basé sur un rôle pour Microsoft Azure à partir de Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Voici un lien vers une vidéo sur Channel 9 de la conférence Ignite MS 2015. Dans cette session, ils parlent accéder aux fonctionnalités de gestion et création de rapports dans Azure et d’Explorer les meilleures pratiques concernant la sécurisation de l’accès aux abonnements Azure à l’aide d’Azure Active Directory.

###<a name="managing-your-storage-account-keys"></a>Gestion de vos clés de compte de stockage

Clés de compte de stockage sont des chaînes de 512 bits créés par Azure qui, ainsi que le nom de compte de stockage, peut être utilisé pour accéder aux objets de données stockées dans le compte de stockage, par exemple, BLOB, entités au sein d’un tableau, des messages file d’attente et des fichiers sur un partage de fichiers Azure. Contrôler l’accès à la stockage compte touches contrôle l’accès au plan de données pour ce compte de stockage.

Chaque compte de stockage comporte deux clés appelés « Clé 1 » et « 2 » dans le [portail Azure](http://portal.azure.com/) et clés dans les applets de commande PowerShell. Celles-ci peuvent être régénérer manuellement à l’aide d’une des méthodes différentes, y compris, mais ne pas limité à l’aide du [portail Azure](https://portal.azure.com/), PowerShell, l’infrastructure du langage commun Azure, ou par programme à l’aide de la bibliothèque de Client de stockage .NET ou l’API REST Azure Storage Services.

Il existe plusieurs raisons régénérer vos clés de compte de stockage.

-   Vous pouvez les régénérer régulièrement pour des raisons de sécurité.

-   Vous devez régénérer vos clés de compte de stockage si une personne réussi à accéder à une application et récupérer la clé qui a été codé en dur ou enregistrés dans un fichier de configuration, en leur donnant un accès total à votre compte de stockage.

-   Un autre cas pour régénérer clé est si votre équipe utilise une application Explorateur de stockage qui conserve la clé de compte de stockage et un des membres de l’équipe quitte. L’application continue de fonctionner, qu’ils puissent accéder à votre compte de stockage après que qu’ils ne disparaissent. Il s’agit réellement la principale raison qu'elles créées au niveau des comptes partagés accès Signatures – vous pouvez utiliser une associations de sécurité au niveau des comptes au lieu de stockage de touches d’accès rapide dans un fichier de configuration.

####<a name="key-regeneration-plan"></a>Plan de régénération des clés

Vous ne voulez pas simplement régénérer la clé que vous utilisez sans une planification. Si vous procédez ainsi, vous pourriez coupé tout accès à ce compte de stockage, qui peut entraîner l’interruption de service importante. C’est pourquoi il existe deux clés. Vous devez régénérer une touche à la fois.

Avant de vous régénérez vos clés, n’oubliez pas que vous disposez d’une liste de toutes vos applications qui ne dépendent du compte de stockage, ainsi que d’autres services que vous utilisez dans Azure. Par exemple, si vous utilisez Azure Media Services qui dépendent de votre compte de stockage, vous devez resynchronisez les touches d’accès avec votre service de support une fois que vous régénérez la clé. Si vous utilisez les applications comme un Explorateur de stockage, vous devrez fournir les clés de nouveau à ces applications ainsi. Notez que si vous avez machines virtuelles dont les fichiers disque dur virtuel sont stockées dans le compte de stockage, ils n’être pas affectés par régénérer les clés de compte de stockage.

Vous pouvez le restaurer vos clés dans le portail Azure. Une fois que clés qu’ils peuvent prendre jusqu'à 10 minutes pour être synchronisé entre les Services de stockage.

Lorsque vous êtes prêt, voici la procédure générale détaillant comment vous devez changer votre clé. Dans ce cas, l’hypothèse est que vous utilisez actuellement clé 1 et que vous vous apprêtez à modifier tout pour utiliser touche 2 à la place.

1.  Régénérer touche 2 pour vous assurer qu’elle est sûre. Vous pouvez le faire dans le portail Azure.

2.  Dans toutes les applications où se trouve la clé de stockage, modifiez la clé de stockage pour utiliser la nouvelle valeur de clé 2. Testez et publiez l’application.

3.  Une fois tous les des applications et services se trouvent au-dessus et fonctionne correctement, régénérer clé 1. Cela garantit que toute personne à laquelle vous n’avez pas accordé expressément la nouvelle clé n’auront plus accès au compte de stockage.

Si vous utilisez actuellement touche 2, vous pouvez utiliser la même procédure, mais inverser les noms de clé.

Vous pouvez déplacer sur deux jours, modification de chaque application à utiliser la nouvelle clé et sa publication. Une fois celles-ci sont exécuté, vous devez puis revenir en arrière et régénérer l’ancienne clé afin qu’il ne fonctionne plus.

Une autre option consiste à placer la clé de compte de stockage dans un [L’archivage sécurisé de clé Azure](https://azure.microsoft.com/services/key-vault/) comme un code secret et avez vos applications de récupérer la clé à partir de là. Puis lorsque vous régénérez la clé et mettre à jour l’archivage sécurisé de clé Azure, les applications pas devront être redéployé, car ils chercher la nouvelle clé de l’archivage sécurisé de clé Azure automatiquement. Notez que vous pouvez avoir l’application de lire la clé de chaque fois que vous en avez besoin, ou vous pouvez mettre en cache en mémoire et en cas d’échec lors de son utilisation, récupérer de nouveau la clé de l’archivage sécurisé de clé Azure.

À l’aide de l’archivage sécurisé de clé Azure ajoute également un autre niveau de sécurité pour les clés de stockage. Si vous utilisez cette méthode, vous aurez jamais la codé en dur clés de stockage dans un fichier de configuration, qui permet de supprimer cette avenue de quelqu'un l’accès aux clés sans autorisation spécifique.

Un autre avantage de l’utilisation de l’archivage sécurisé de clé Azure est que vous pouvez également contrôler l’accès à vos clés à l’aide d’Azure Active Directory. Cela signifie que vous pouvez accorder l’accès à la poignée d’applications que vous avez besoin pour récupérer les clés de l’archivage sécurisé de clé Azure et savoir que d’autres applications ne seront pas en mesure d’accéder à l’aide des touches sans leur accorder l’autorisation spécifiquement.

Remarque : il est recommandé d’utiliser uniquement une des clés dans l’ensemble de vos applications en même temps. Si vous utilisez clé 1 à certains endroits et touche 2 dans d’autres personnes, vous ne pourrez pas faire pivoter vos clés sans une application perdre l’accès.

####<a name="resources"></a>Ressources

-   [À propos des comptes de stockage Azure](storage-create-storage-account.md#regenerate-storage-access-keys)

    Cet article offre une vue d’ensemble des comptes de stockage et traite de l’affichage, la copie et régénérer les touches d’accès de stockage.

-   [Référence des API REST stockage Azure ressource fournisseur](https://msdn.microsoft.com/library/mt163683.aspx)

    Cet article contient des liens vers des articles spécifiques sur récupérer les clés de compte de stockage et régénérer les clés de compte de stockage pour un compte Azure à l’aide de l’API REST. Remarque : Il s’agit des comptes de stockage Gestionnaire de ressources.

-   [Opérations sur les comptes de stockage](https://msdn.microsoft.com/library/ee460790.aspx)

    Cet article dans le Gestionnaire de Service de stockage reste API Reference contient des liens vers des articles spécifiques sur récupération et régénérer les clés de compte de stockage à l’aide de l’API REST. Remarque : Il s’agit pour les comptes de stockage classique.

-   [Dire au revoir pour la gestion de clés : gérez l’accès aux données de stockage Azure utilise Azure Active Directory](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

    Cet article vous explique comment utiliser Active Directory pour contrôler l’accès à vos clés de stockage Azure dans l’archivage sécurisé de clé Azure. Il indique également comment utiliser une tâche Automation Azure régénérer les clés de toutes les heures.

##<a name="data-plane-security"></a>Sécurité de plan de données

Sécurité de plan de données fait référence aux méthodes utilisés pour sécuriser les objets de données stockées dans le stockage Azure – des objets BLOB, des files d’attente, des tableaux et des fichiers. Nous l’avons vu méthodes pour chiffrer les données et la sécurité durant le transfert des données, mais comment procéder à l’autorisation d’accès aux objets ?

Il existe deux méthodes pour contrôler l’accès aux objets données eux-mêmes. La première est en contrôlant l’accès aux clés de compte de stockage et le second utilise partagés accès Signatures pour accorder l’accès aux objets de données spécifique pour une durée spécifique.

Une exception à noter est que vous pouvez autoriser l’accès public à vos objets BLOB en définissant le niveau d’accès pour le conteneur qui contient les objets BLOB en conséquence. Si vous définissez des accès pour un conteneur d’objets Blob ou un conteneur, il autorise l’accès en lecture public pour les objets BLOB dans ce conteneur. Cela signifie que toute personne disposant d’une URL pointant vers un blob dans ce conteneur puisse l’ouvrir dans un navigateur sans à l’aide d’une Signature accès partagés ou les touches de compte de stockage.

###<a name="storage-account-keys"></a>Clés de compte de stockage

Clés de compte de stockage sont des chaînes de 512 bits créés par Azure qui, ainsi que le nom de compte de stockage, peut être utilisé pour accéder aux objets de données stockées dans le compte de stockage.

Par exemple, vous pouvez lire des objets BLOB, écrire aux files d’attente, créer des tables et modifier des fichiers. La plupart de ces actions peuvent être effectuées via le portail d’Azure, ou en utilisant un grand nombre d’applications Explorateur de stockage. Vous pouvez également écrire du code pour utiliser l’API REST ou un des bibliothèques du Client de stockage pour effectuer ces opérations.

Comme indiqué dans la section sur la [Sécurité du plan d’administration](#management-plane-security), accès aux clés de stockage pour un compte de stockage classique peuvent être accordé en donnant un accès total à l’abonnement Azure. Accès aux clés de stockage pour un compte de stockage à l’aide du modèle Azure le Gestionnaire de ressources peut être contrôlé via un contrôle d’accès basé sur un rôle (RBAC).

###<a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Comment faire pour l’accès aux objets de votre compte à l’aide de partagés accès Signatures et stockées les stratégies d’accès délégué

Une Signature accès partagé est une chaîne contenant un jeton de sécurité qui peut être associé à un URI qui permet l’accès aux objets de stockage délégué et spécifier des contraintes telles que les autorisations et la plage de date/heure d’accès.

Vous pouvez accorder l’accès aux tables, conteneurs, file d’attente messages, fichiers et des objets BLOB. Avec des tableaux, vous pouvez effectivement accorder autorisé à accéder à une plage d’entités dans le tableau en spécifiant les plages partition et ligne sur lequel vous souhaitez que l’utilisateur à accéder. Par exemple, si vous avez des données stockées avec une clé de partition d’état géographique, vous pouvez octroyer l’accès à uniquement les données pour Californie.

Dans un autre exemple, éventuellement donner une application web un jeton associations de sécurité qui permet d’écrire des entrées à une file d’attente et donner un collaborateur rôle application un jeton associations de sécurité pour obtenir des messages de la file d’attente et les traiter. Ou vous pouvez donner à un client un jeton de sa qu’ils peuvent utiliser pour télécharger des images sur un conteneur dans le stockage Blob et autoriser une application web à lire ces images. Dans les deux cas, il existe une séparation des problèmes – chaque application peut être donnée simplement le niveau d’accès dont ils ont besoin pour effectuer leur tâche. Cela est possible grâce à l’utilisation de Signatures d’accès partagé.

####<a name="why-you-want-to-use-shared-access-signatures"></a>Raison pour laquelle vous souhaitez utiliser partagés accès Signatures

Pourquoi devrais-je utiliser un sa au lieu de simplement communiquez votre clé de compte de stockage, qui est beaucoup plus facile ? Communiquez votre clé de compte de stockage est vous aimez donner les clés de votre kingdom de stockage. Qui autorise l’accès complet. Une personne peut utiliser vos clés et téléchargez leur bibliothèque de musique à votre compte de stockage. Ils peuvent également remplacer vos fichiers avec les versions infection antivirus ou subtiliser vos données. Donnant un accès illimité absent (e) à votre compte de stockage est un élément qui ne doivent pas être prises légèrement.

Signatures d’accès partagé, vous pouvez donner un client uniquement les autorisations requises pour une période limitée. Par exemple, si une personne est Téléchargez un blob à votre compte, vous pouvez leur accorder un accès en écriture simplement suffisamment de temps télécharger le blob (selon la taille du blob, bien entendu). Et si vous changez d’avis, vous pouvez révoquer par access.

En outre, vous pouvez spécifier que les demandes effectuées à l’aide d’une associations de sécurité sont limités à une adresse IP ou une plage d’adresses IP externe à Azure. Vous pouvez également exiger que les requêtes sont effectuées à l’aide d’un protocole spécifique (HTTPS ou HTTP/HTTPS). Cela signifie que si vous souhaitez uniquement autoriser le trafic HTTPS, vous pouvez définir le protocole requis pour HTTPS uniquement et le trafic HTTP est bloqué.

####<a name="definition-of-a-shared-access-signature"></a>Définition d’une Signature accès partagé

Une Signature accès partagé est un ensemble de paramètres de requête ajoutée à l’URL pointant sur la ressource

qui fournit des informations sur l’accès autorisé et la durée dont l’accès est autorisé. Voici un exemple ; Cet URI donne accès en lecture à un blob de cinq minutes. Remarque associations de sécurité des paramètres de requête doit être codé URL, telles que 3 % de deux-points ( :)) ou 20 % de l’espace.

    http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
    ?sv=2015-04-05 (storage service version)
    &st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
    &se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
    &sr=b (resource is a blob)
    &sp=r (read access)
    &sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
    &spr=https (only allow HTTPS requests)
    &sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)

####<a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>Comment la Signature accès partagé est authentifiée par le Service de stockage Azure

Lorsque le service de stockage reçoit la requête, elle accepte les paramètres de requête d’entrée et crée une signature à l’aide de la même méthode que le programme appelant. Il compare ensuite les deux signatures. Si elles s’accordent, le service de stockage pouvez vérifier la version du service de stockage pour vous assurer qu’il est valide, vérifiez que la date et l’heure correspondent au sein de la fenêtre spécifiée, vérifiez que l’accès demandé correspond à la demande, etc..

Par exemple, grâce à notre URL ci-dessus, si l’URL a été pointant vers un fichier à la place un blob, cette requête échoue, car elle spécifie que la Signature accès partagé est destiné à un blob. Si la commande reste appelée a été mise à jour un blob, il échoue, car la Signature accès partagé indique que l’accès en lecture seule est autorisé.

####<a name="types-of-shared-access-signatures"></a>Types de Signatures accès partagé

-   Une associations de sécurité au niveau de service peuvent servir à accéder à des ressources spécifiques dans un compte de stockage. Quelques exemples de ce sont récupération d’une liste des objets BLOB dans un conteneur, le téléchargement d’un blob, mise à jour une entité dans un tableau, ajout de messages à une file d’attente ou télécharger un fichier vers un partage de fichiers.

-   Une associations de sécurité au niveau des comptes peuvent servir à susceptibles d’une associations de sécurité au niveau de service peuvent être utilisée pour accéder à. Par ailleurs, il peut vous fournir options vers des ressources qui ne sont pas autorisées avec une associations de sécurité au niveau de service, telles que la possibilité de créer des conteneurs, des tableaux, des files d’attente et les partages de fichiers. Vous pouvez également spécifier l’accès à plusieurs services en même temps. Par exemple, vous pouvez donner une personne l’accès à des objets BLOB et des fichiers dans votre compte de stockage.

####<a name="creating-an-sas-uri"></a>Création d’un URI associations de sécurité

1.  Vous pouvez créer un URI ad hoc à la demande, tous les paramètres de requête définir chaque fois.

    Il s’agit réellement flexible, mais si vous avez un ensemble logique de paramètres qui sont similaires à chaque fois, à l’aide d’une stratégie d’accès stockées est préférable.

2.  Vous pouvez créer une stratégie d’accès de stockage pour un conteneur entier, un partage de fichiers, un tableau ou un file d’attente. Vous pouvez utiliser ce comme base pour les URI associations de sécurité que vous créez. Autorisations basées sur les stratégies d’accès stocké peuvent être révoquées facilement. Vous pouvez avoir jusqu'à 5 stratégies définies sur chaque conteneur, la file d’attente, le tableau ou le partage de fichiers.

    Par exemple, si vous souhaitez que les personnes de lire les objets BLOB dans un conteneur spécifique, vous pouvez créer une stratégie d’accès stockées indiquant « accorder l’accès en lecture » et les autres paramètres qui seront les mêmes chaque fois. Vous pouvez créer un URI SAS en utilisant les paramètres de la stratégie d’accès de stockage et en spécifiant la date/heure d’expiration. L’avantage de cela est que vous n’êtes pas obligé de spécifier tous les paramètres de requête à chaque fois.

####<a name="revocation"></a>Révocation de certificats

Supposons que vos sa a été déchiffré, ou que vous souhaitez modifier en raison de sécurité de l’entreprise ou aux exigences de conformité réglementaire. Comment révoquer l’accès à une ressource à l’aide de ce associations de sécurité ? Tout dépend de comment vous avez créé l’URI SAS.

Si vous n’utilisez pas d’URI ad hoc, vous disposez de trois options. Vous pouvez émettre des jetons associations de sécurité avec les stratégies d’expiration bref et attendez simplement que les associations de sécurité le point d’expirer. Vous pouvez renommer ou supprimer la ressource (en supposant que le jeton a été portée à un seul objet). Vous pouvez modifier les clés de compte de stockage. Cette dernière option peut avoir un impact volumineux, en fonction du nombre de services est à l’aide de ce compte de stockage et probablement n’est pas ce que vous souhaitez faire sans une planification.

Si vous utilisez un sa dérivé d’une stratégie d’accès de stockage, vous pouvez supprimer l’accès par retrait la stratégie d’accès de stockage : vous pouvez simplement modifier afin qu’il a déjà expiré, ou vous pouvez le supprimer complètement. Cela prend effet immédiatement et invalide chaque associations de sécurité créées à l’aide de cette stratégie d’accès de stockage. Mise à jour ou suppression de la stratégie d’accès stockées il est possible que la table personnes impact sur l’accès à ce conteneur spécifique, le partage de fichiers, ou file d’attente via sa, mais si les clients sont écrits afin qu’ils demandent une nouvelles associations de sécurité lorsque l’ancien deviendrait non valide, cela fonctionne correctement.

Étant donné qu’à l’aide d’un sa dérivé d’une stratégie d’accès stockées vous donne la possibilité pour révoquer que sa immédiatement, il est recommandé de toujours utiliser les stratégies d’accès stockées lorsque cela est possible.

####<a name="resources"></a>Ressources

Pour plus d’informations sur l’utilisation de Signatures de Access partagés et stockés les stratégies d’accès, avec des exemples, consultez les articles suivants :

-   Voici les articles de référence.

    -   [Service associations de sécurité](https://msdn.microsoft.com/library/dn140256.aspx)

        Cet article fournit des exemples d’utilisation d’une associations de sécurité au niveau de service avec des objets BLOB, messages file d’attente, plages de table et des fichiers.

    -   [Construction d’un service associations de sécurité](https://msdn.microsoft.com/library/dn140255.aspx)

    -   [Construction d’un compte associations de sécurité](https://msdn.microsoft.com/library/mt584140.aspx)

-   Il s’agit des didacticiels pour l’utilisation de la bibliothèque cliente .NET pour créer des Signatures de Access partagés et stockés les stratégies d’accès.

    -   [Utilisation de Signatures accès partagé (sa)](storage-dotnet-shared-access-signature-part-1.md)

    -   [Partagé des Signatures Access, partie 2 : Créer et utiliser une associations de sécurité avec le Service d’objets Blob](storage-dotnet-shared-access-signature-part-2.md)

        Cet article inclut une explication du modèle associations de sécurité, exemples de Signatures d’accès partagé, et utilisent des recommandations pour nous vous recommandons d’associations de sécurité. Vous trouverez également la révocation de l’autorisation.

-   Limiter l’accès par adresse IP (IP utilisateurs)

    -   [Qu’est un point de terminaison liste de contrôle d’accès (utilisateurs) ?](../virtual-network/virtual-networks-acl.md)

    -   [Construction d’un Service associations de sécurité](https://msdn.microsoft.com/library/azure/dn140255.aspx)

        Il s’agit de l’article de référence pour les associations de sécurité au niveau de service ; Il inclut un exemple de ACLing IP.

    -   [Construction d’un compte associations de sécurité](https://msdn.microsoft.com/library/azure/mt584140.aspx)

        Il s’agit de l’article de référence pour les associations de sécurité au niveau des comptes ; Il inclut un exemple de ACLing IP.

-   Authentification

    -    [Authentification pour les Services de stockage Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)

-   Partagé des Signatures Access didacticiels de mise en route

    -   [SA mise en route didacticiel](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

##<a name="encryption-in-transit"></a>Chiffrement lors des transferts

###<a name="transport-level-encryption--using-https"></a>Chiffrement de niveau transport – à l’aide de HTTPS

Une autre chose à faire pour assurer la sécurité de vos données de stockage Azure consiste à chiffrer les données entre le client et le stockage Azure. La première recommandation consiste à toujours utiliser le protocole [HTTPS](https://en.wikipedia.org/wiki/HTTPS) , qui permet de sécuriser la communication via Internet public.

Vous devez toujours utiliser HTTPS lors de l’appel à l’API REST ou accéder à des objets dans le stockage. En outre, **Partagés accès Signatures**, qui peuvent servir à déléguer l’accès aux objets de stockage Azure, inclure une option pour indiquer que seul le protocole HTTPS peut être utilisé lorsque vous utilisez partagés accès Signatures, s’assurer que tout le monde envoyant des liens avec jetons sa utilisera le protocole approprié.

####<a name="resources"></a>Ressources

-   [Activer HTTPS pour une application dans le Service d’application Azure](../app-service-web/web-sites-configure-ssl-certificate.md)

    Cet article vous explique comment activer HTTPS pour une application Web Azure.

###<a name="using-encryption-during-transit-with-azure-file-shares"></a>Avec le chiffrement pendant son transfert et les partages de fichiers Azure

Stockage de fichiers Azure prend en charge HTTPS lors de l’utilisation de l’API REST, mais sont plus couramment utilisés comme un partage de fichiers PME associé à une machine virtuelle. 2.1 PME ne pas en charge le chiffrement, les connexions sont autorisées uniquement dans la même région dans Azure. Cependant, 3.0 PME prend en charge le chiffrement et peut être utilisé avec Windows Server 2012 R2, Windows 8, Windows 8.1 et Windows 10, ce qui permet d’effectuer région accéder et même access sur le bureau.

Notez que si les partages de fichiers Azure peut être utilisées avec Unix, le client Linux SMB ne pas encore prend en charge le chiffrement, afin seulement l’accès est autorisé au sein d’une région Azure. Prise en charge le chiffrement de Linux se trouve sur la feuille de route de développeurs Linux responsables de la fonctionnalité de PME. Lorsqu’ils ajoutent chiffrement, vous avez la possibilité de même pour accéder à un partage de fichiers Azure sur Linux comme vous le feriez pour Windows.

####<a name="resources"></a>Ressources

-   [Comment utiliser le stockage de fichiers Azure avec Linux](storage-how-to-use-files-linux.md)

    Cet article vous explique comment monter un partage de fichiers Azure sur un système Linux et télécharger des fichiers.

-   [Prise en main le stockage de fichiers Azure sur Windows](storage-dotnet-how-to-use-files.md)

    Cet article fournit une vue d’ensemble des partages de fichiers Azure et comment monter et les utiliser à l’aide de PowerShell et .NET.

-   [Stockage de fichiers Azure à l’intérieur](https://azure.microsoft.com/blog/inside-azure-file-storage/)

    Cet article annonce la disponibilité générale de stockage de fichiers Azure et fournit des informations techniques sur le chiffrement PME 3.0.

###<a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Utilisation du chiffrement côté Client pour sécuriser les données que vous envoyez au stockage

Une autre option qui vous permet de vous assurer que vos données soient sécurisées pendant transférées entre une application cliente et stockage est le chiffrement côté Client. Les données sont chiffrées avant d’être transférées dans le stockage Azure. Lorsque vous récupérez les données à partir du stockage Azure, les données sont déchiffrées après réception sur le côté client. Même si les données sont chiffrées accédant sur le réseau, nous vous recommandons que vous utilisez également HTTPS, car celui-ci contient des contrôles d’intégrité des données intégrés qui vous aider à limiter les erreurs de réseau qui affectent l’intégrité des données.

Chiffrement côté client est également une méthode de chiffrement vos données inactives, comme les données sont stockées dans sa forme chiffré. Nous aborderons cela plus en détail dans la section chiffrement [inactives](#encryption-at-rest).

##<a name="encryption-at-rest"></a>Chiffrement inactives

Il existe trois fonctionnalités Azure permettant de chiffrement inactives. Chiffrement de disque Azure est utilisé pour chiffrer les disques du système d’exploitation et les données dans le Machines virtuelles IaaS. Les deux autres – le chiffrement côté Client et SSE – sont tous deux utilisés pour chiffrer les données dans le stockage Azure. Nous allons étudier chacune de ces éléments, puis effectuez une comparaison et voir lorsque chacun d'entre eux peut être utilisé.

Vous pouvez utiliser le chiffrement côté Client pour chiffrer les données lors des transferts (qui est également stocké dans sa forme cryptée dans le stockage), vous pouvez simplement utiliser HTTPS lors du transfert et disposer d’un moyen pour les données à chiffrer automatiquement lorsqu’elles sont stockées. Il existe deux manières de procéder--Azure disque chiffrement et SSE. Une est utilisée pour chiffrer directement les données de système d’exploitation et données disques utilisés par les machines virtuelles, et l’autre est utilisé pour chiffrer les données écrites sur le stockage d’objets Blob Azure.

###<a name="storage-service-encryption-sse"></a>Chiffrement de Service de stockage (SSE)

SSE vous permet de demander que le service de stockage chiffrer automatiquement les données lors de l’écriture au stockage Azure. Lorsque vous lisez les données à partir du stockage Azure, il sera déchiffré avant d’être retourné par le service de stockage. Cela vous permet de protéger vos données sans avoir à modifier le code ou ajouter du code à toutes les applications.

Il s’agit d’un paramètre qui s’applique au compte de stockage entier. Vous pouvez activer et désactiver cette fonctionnalité en modifiant la valeur du paramètre. Pour ce faire, vous pouvez utiliser le portail Azure, PowerShell, l’infrastructure du langage commun Azure, l’API REST de stockage ressource fournisseur ou la bibliothèque .NET stockage Client. Par défaut, SSE est désactivé.

Pour l’instant, les clés utilisées pour le chiffrement sont gérés par Microsoft. Nous générer les clés à l’origine et de gérer le stockage sécurisé de l’aide des touches ainsi que la rotation régulière telle que définie par politique interne de Microsoft. À l’avenir, vous obtenez la possibilité de gérer vos propres clés de chiffrement et fournir un chemin d’accès de migration à partir des clés gérés par Microsoft aux clés gérés par le client.

Cette fonctionnalité est disponible pour les comptes Standard et Premium stockage créés à l’aide du modèle de déploiement du Gestionnaire de ressources. SSE s’applique uniquement pour bloquer les objets BLOB, des objets BLOB de la page et ajouter des objets BLOB. Les autres types de données, y compris les tables, les files d’attente et les fichiers ne seront pas chiffrées.

Données sont chiffrées uniquement lorsque SSE est activée et les données soient écrites au stockage d’objets Blob. Activation ou désactivation SSE n’influe pas sur les données existantes. En d’autres termes, lorsque vous activez cette chiffrement, il ne sera pas revenir en arrière et chiffrer les données qui existe déjà ; ni s’il déchiffrer les données qui existe déjà lorsque vous désactivez SSE.

Si vous voulez utiliser cette fonctionnalité avec un compte de stockage classique, vous pouvez créer un nouveau compte de stockage du Gestionnaire de ressources et AzCopy permet de copier les données vers le nouveau compte. 

###<a name="client-side-encryption"></a>Chiffrement côté client

Nous avons dit chiffrement côté client lorsque vous abordez le chiffrement des données lors des transferts. Cette fonctionnalité permet de par programme chiffrer vos données dans une application cliente avant d’envoyer la transmission soient écrites dans le stockage Azure et par programme déchiffrer vos données après récupération à partir du stockage Azure.

Cela fournit chiffrement lors des transferts, mais il fournit également la fonctionnalité de chiffrement inactives. Notez que bien que les données sont chiffrées lors des transferts, nous recommandons l’utilisation de HTTPS pour tirer parti des contrôles de l’intégrité des données intégrés qui permettent d’atténuer les erreurs de réseau qui affectent l’intégrité des données.

Exemple de l’endroit où vous pouvez utiliser ce est si vous disposez d’une application web qui stocke des objets BLOB et récupère des objets BLOB et que vous voulez l’application et données être aussi sécurisé que possible. Dans ce cas, vous utiliserez le chiffrement côté client. Le trafic entre le client et le Service d’objets Blob Azure contient la ressource chiffrée, et personne ne peut interpréter les données lors des transferts et reconstituer dans vos objets BLOB privés.

Chiffrement côté client est intégré à Java et les bibliothèques de client de stockage .NET, qui à son tour utilisent Azure clé l’archivage sécurisé API, rendant facile pour mettre en œuvre. Le processus de chiffrer et déchiffrer les données utilise la technique d’enveloppe et stocke les métadonnées utilisées par le chiffrement dans chaque objet de stockage. Par exemple, pour les objets BLOB, il stocke dans les métadonnées blob, tandis que pour les files d’attente, elle ajoute à chaque message file d’attente.

Pour le chiffrement lui-même, vous pouvez générer et gérer vos propres clés de chiffrement. Vous pouvez également utiliser les touches générées par la bibliothèque de Client de stockage Azure, ou vous pouvez avoir l’archivage sécurisé de clé Azure générer les clés. Vous pouvez stocker vos clés de chiffrement dans votre espace de stockage clés en local, ou vous pouvez les stocker dans un archivage sécurisé de clé Azure. L’archivage sécurisé clé Azure permet de vous octroyer l’accès aux secrets dans l’archivage sécurisé de clé Azure à des utilisateurs spécifiques à l’aide d’Azure Active Directory. Cela signifie que pas seulement tout le monde peut lire l’archivage sécurisé de clé Azure et récupérer les clés que vous utilisez pour le chiffrement côté client.

####<a name="resources"></a>Ressources

-   [Chiffrer et déchiffrer des objets BLOB Microsoft Azure Storage à l’aide de l’archivage sécurisé de clé Azure](storage-encrypt-decrypt-blobs-key-vault.md)

    Cet article vous explique comment utiliser le chiffrement côté client avec l’archivage sécurisé clé Azure, notamment comment créer le KEK et le stocker dans l’archivage sécurisé à l’aide de PowerShell.

-   [Le chiffrement côté client et Azure l’archivage sécurisé clé pour le stockage de Microsoft Azure](storage-client-side-encryption.md)

    Cet article donne une explication du chiffrement côté client et fournit des exemples d’utilisation de la bibliothèque de stockage client pour chiffrer et déchiffrer des ressources à partir des services de stockage quatre. Il parle également l’archivage sécurisé de clé Azure.

###<a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Utilisation du chiffrement de disque Azure pour chiffrer disques utilisés par vos machines virtuelles

Le chiffrement des disques Azure est une nouvelle fonctionnalité qui est en cours d’aperçu. Cette fonctionnalité permet de chiffrer les disques du système d’exploitation et données utilisée par une Machine virtuelle IaaS. Pour Windows, les lecteurs sont chiffrés à l’aide de la technologie de chiffrement BitLocker standard. Pour Linux, les disques sont chiffrés à l’aide de la technologie DM Crypt. Ceci est intégré à l’archivage sécurisé de clé Azure à vous permettent de contrôler et de gérer les clés de chiffrement de disque.

La solution de chiffrement de disque Azure prend en charge les scénarios de chiffrement trois client suivants :

-   Activer le chiffrement de nouvelles IaaS machines virtuelles créé à partir de fichiers de disque dur virtuel chiffrées client et les clés de chiffrement fournie par le client, qui sont stockées dans l’archivage sécurisé de clé Azure.

-   Activer le chiffrement de nouvelles IaaS machines virtuelles créé à partir de la Azure Marketplace.

-   Activer le chiffrement de machines virtuelles IaaS existantes déjà en cours d’exécution dans Azure.

>[AZURE.NOTE] Pour les ordinateurs virtuels Linux déjà en cours d’exécution dans Azure ou nouvelles Linux machines virtuelles créé à partir d’images sur le marché Azure, le chiffrement du disque du système d’exploitation n’est pas actuellement pris en charge. Chiffrement du Volume du système d’exploitation pour les machines virtuelles Linux est pris en charge uniquement pour les machines virtuelles qui ont été chiffrés en local et téléchargement dans Azure. Cette limitation s’applique uniquement sur le disque du système d’exploitation ; chiffrement des volumes de données pour un Linux VM est pris en charge.

La solution prend en charge les éléments suivants pour les machines virtuelles IaaS pré-version publique lorsque activée dans Microsoft Azure :

-   Intégration avec l’archivage sécurisé clé Azure

-   Standard [A, D et G série IaaS machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/)

-   Activer le chiffrement de machines virtuelles IaaS créé à l’aide du modèle du [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md)

-   Public Azure toutes les [régions](https://azure.microsoft.com/regions/)

Cette fonctionnalité garantit que toutes les données sur vos disques machine virtuelle sont chiffrées au reste dans le stockage Azure.

####<a name="resources"></a>Ressources

-   [Chiffrement de disque Azure pour Windows et Machines virtuelles Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

    Cet article décrit la version préliminaire de chiffrement des disques Azure et fournit un lien pour télécharger le livre blanc.

###<a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Comparaison de chiffrement des disques Azure, SSE et le chiffrement côté Client

####<a name="iaas-vms-and-their-vhd-files"></a>Machines virtuelles IaaS et ses fichiers de disque dur virtuel

Pour disques utilisés par les machines virtuelles IaaS, nous vous recommandons de l’utilisation du chiffrement de disque Azure. Vous pouvez activer SSE pour chiffrer les fichiers de disque dur virtuel qui sont utilisés pour sauvegarder ces disques dans le stockage Azure, mais il chiffre uniquement les données nouvellement écrites. Cela signifie que si vous créez une machine virtuelle, puis activez SSE sur le compte de stockage qui contient le fichier de disque dur virtuel, seules les modifications seront chiffrées, pas le fichier de disque dur virtuel d’origine.

Si vous créez une machine virtuelle à l’aide d’une image à partir du Azure Marketplace, Azure effectue une [copie partielle](https://en.wikipedia.org/wiki/Object_copying) de l’image à votre compte de stockage dans le stockage Azure, et même si vous avez SSE activé n’est pas chiffré. Une fois qu’il crée la machine virtuelle et une mise à jour de l’image, SSE démarre le chiffrement des données. Pour cette raison, il est préférable d’utiliser le chiffrement des disques Azure sur machines virtuelles créés à partir d’images sur le marché Azure si vous souhaitez entièrement chiffré.

Si vous importez une machine virtuelle préalable chiffrée dans Azure locales, vous ne pourrez pas télécharger les clés de chiffrement dans l’archivage sécurisé de clé Azure et continuer à utiliser le chiffrement pour cet ordinateur virtuel que vous utilisiez en local. Chiffrement de disque Azure est activé pour traiter ce scénario.

Si vous avez dur non chiffré locales, vous pouvez télécharger dans la galerie comme une image personnalisée et configurer une machine virtuelle à partir de celui-ci. Si ce faire, utilisez les modèles de gestionnaire de ressources, vous pouvez demander à activer le chiffrement de disque Azure lorsqu’il démarre la machine virtuelle.

Lorsque vous ajoutez un disque de données et montez dans la machine virtuelle, vous pouvez activer le chiffrement des disques Azure qu’il contient des données. Il sera chiffrer ce disque données localement tout d’abord, et puis la couche de gestion du service fera une écriture différée par rapport à l’espace de stockage afin que le contenu de stockage est chiffré.

####<a name="client-side-encryption"></a>Chiffrement côté client####

Chiffrement côté client est la méthode la plus sûre de chiffrer vos données, car elle chiffre avant de voies et chiffre les données au repos. Toutefois, elle ne requiert que vous ajouter du code à vos applications à l’aide de stockage, vous souhaiterez ne peut-être pas faire. Dans ce cas, vous pouvez utiliser HTTPs pour vos données échangées et SSE pour chiffrer les données au reste.

Avec le chiffrement côté client, vous pouvez chiffrer entités de table, les messages de file d’attente et des objets BLOB. Avec SSE, vous pouvez chiffrer uniquement des objets BLOB. Si vous avez besoin des données de tables et file d’attente d’être chiffrées, vous devez utiliser le chiffrement côté client.

Chiffrement côté client est entièrement géré par l’application. Cette approche est la plus sûre, mais ne nécessite que vous apportez des modifications par programme à votre application et mettre en place des processus de gestion de clés. Vous utiliseriez ceci lorsque vous souhaitez la sécurité supplémentaire pendant son transfert, et vous voulez que vos données stockées à chiffrer.

Chiffrement côté client est plus charge sur le client, et vous devez tenir compte de vos plans extensibilité élevées, surtout si vous êtes le chiffrement et transfert d’un grand nombre de données.

####<a name="storage-service-encryption-sse"></a>Chiffrement de Service de stockage (SSE)

SSE est géré par le stockage Azure. L’utilisation de SSE ne fournit pas pour la sécurité des données lors des transferts, mais elle chiffre les données telle qu’elle est écrite au stockage Azure. Il n’existe aucun impact sur les performances lors de l’utilisation de cette fonctionnalité.

Vous pouvez uniquement chiffrer des objets BLOB bloc, ajouter des objets BLOB et des objets BLOB SSE à l’aide de la page. Si vous avez besoin chiffrer les données de la table ou données file d’attente, envisagez d’utiliser le chiffrement côté client.

Si vous avez une archive ou une bibliothèque de fichiers de disque dur virtuel que vous utilisez comme base pour la création de nouvelles machines virtuelles, vous pouvez créer un nouveau compte de stockage, autoriser SSE et puis télécharger ces fichiers à ce compte. Ces fichiers de disque dur virtuel seront chiffrés par le stockage Azure.

Si vous avez activé pour les disques dans une machine virtuelle et SSE activé pour le compte de stockage contenant les fichiers du disque dur virtuel le chiffrement des disques Azure, il fonctionne correctement ; il générera de toutes les données que vous venez écrit en cours de chiffrement à deux reprises.

##<a name="storage-analytics"></a>Stockage Analytique

###<a name="using-storage-analytics-to-monitor-authorization-type"></a>À l’aide de stockage Analytique pour contrôler le type d’autorisation

Pour chaque compte de stockage, vous pouvez activer Analytique de stockage Azure effectuer la journalisation et stocker des données de métrique. Ceci est un excellent outil à utiliser lorsque vous souhaitez vérifier les mesures de performances d’un compte de stockage, ou résoudre un compte de stockage, car vous rencontrez des problèmes de performances.

Un autre élément de données que vous pouvez voir dans les journaux analytique stockage est la méthode d’authentification utilisée par une personne lorsqu’ils accèdent à stockage. Par exemple, avec le stockage Blob, vous pouvez voir si elles utilisé une Signature accès partagés ou les touches de compte de stockage, ou si le blob accessible a été public.

Cela peut être très utile si vous soyez étroitement protégeant l’accès au stockage. Par exemple, dans le stockage Blob vous pouvez définir tous les conteneurs en privé et implémenter l’utilisation d’un service associations de sécurité dans l’ensemble de vos applications. Vous pouvez vérifier les journaux régulièrement pour voir si vos objets BLOB est accessibles via les clés de compte de stockage, qui peuvent signaler une violation de sécurité, ou si les objets BLOB sont publics, mais elles ne doivent pas être.

####<a name="what-do-the-logs-look-like"></a>Quoi ressemblent les journaux ?

Une fois que vous activez les indicateurs de compte de stockage et journalisation via le portail d’Azure, données analytique commence par accumuler rapidement. La journalisation et les mesures pour chaque service est différente ; la journalisation écrit uniquement lorsqu’il y a activité dans ce compte de stockage, tandis que les mesures seront enregistrés toutes les minutes, toutes les heures ou tous les jours, en fonction de sa configuration.

Les journaux sont stockés dans des objets BLOB bloc dans un conteneur nommé $logs dans le compte de stockage. Ce conteneur est créé automatiquement lorsque le stockage Analytique est activé. Une fois ce conteneur est créé, vous ne pouvez pas supprimer, bien que vous pouvez supprimer son contenu.

Sous le conteneur $logs, il existe un dossier pour chaque service et il n’y a sous-dossiers pour l’année/mois/jour/heure. Sous heure, les journaux sont simplement numérotées. Voici à quoi ressemblera la structure du répertoire :

![Affichage des fichiers journaux](./media/storage-security-guide/image1.png)

Chaque demande au stockage Azure est enregistrée. Voici un aperçu d’un fichier journal, montrant les premiers champs peu.

![Instantané d’un fichier journal](./media/storage-security-guide/image2.png)

Vous pouvez voir que vous pouvez utiliser les journaux pour effectuer le suivi de tout type d’appels vers un compte de stockage.

####<a name="what-are-all-of-those-fields-for"></a>Quelles sont tous ces champs pour ?

Il existe un article répertorié dans les ressources ci-dessous qui fournit la liste des champs dans les journaux et qu’elles sont utilisées pour plusieurs. Voici la liste des champs dans l’ordre :

![Instantané de champs dans un fichier journal](./media/storage-security-guide/image3.png)

Nous sommes intéressés par les entrées de GetBlob et la manière dont elles sont authentifiés, afin que nous avons besoin de rechercher les entrées avec le type d’opération « Get-Blob » et vérifiez l’état de requête (4<sup>ème</sup> colonne) et le type d’autorisation (8<sup>ème</sup> colonne).

Par exemple, dans les premières lignes dans la liste ci-dessus, l’état de la demande est « Réussite » et le type d’autorisation est « authentifié ». Cela signifie que la demande a été validée à l’aide de la clé de compte de stockage.

####<a name="how-are-my-blobs-being-authenticated"></a>Comment mon BLOB est authentifiés ?

Nous avons trois cas qui nous intéresse.

1.  Le blob est public et est accessible à l’aide d’une URL sans Signature accès partagé. Dans ce cas, l’état de la demande est « AnonymousSuccess » et le type d’autorisation est « anonyme ».

    1.0 ; 2015-11-17T02:01:29.0488963Z ; GetBlob ; **AnonymousSuccess**; 200 ; 124 37 ; **anonyme**; mystorage...

2.  Le blob est privé et a été utilisé avec une Signature accès partagé. Dans ce cas, l’état de la demande est « SASSuccess » et le type d’autorisation est « sa ».

    1.0 ; 2015-11-16T18:30:05.6556115Z ; GetBlob ; **SASSuccess**; 200 ; 416 64 ; **associations de sécurité**; mystorage...

3.  Le blob est privé et la clé de stockage a été utilisée pour y accéder. Dans ce cas, l’état de la demande est «**Réussite**» et le type d’autorisation est «**authentifié**».

    1.0 ; 2015-11-16T18:32:24.3174537Z ; GetBlob ; **Succès**; 206 ; 59 22 ; **authentifié**; mystorage...

Vous pouvez utiliser l’Analyseur de Message de Microsoft pour afficher et analyser ces fichiers journaux. Il inclut les fonctionnalités de recherche et de filtrage. Par exemple, vous souhaiterez peut-être rechercher des instances de GetBlob pour voir si l’utilisation est ce que vous attendiez, par exemple, pour vous assurer que quelqu'un pas accède à votre compte de stockage inappropriée.

####<a name="resources"></a>Ressources

-   [Stockage Analytique](storage-analytics.md)

    Cet article est une vue d’ensemble des analytique de stockage et comment les activer.

-   [Format de stockage du journal Analytique](https://msdn.microsoft.com/library/azure/hh343259.aspx)

    Cet article montre le Format du journal stockage Analytique et détails les champs disponibles, notamment le type d’authentification, qui indique le type d’authentification utilisé pour la demande.

-   [Analyser un compte de stockage dans le portail Azure](storage-monitor-storage-account.md)

    Cet article vous explique comment configurer la journalisation pour un compte de stockage et de contrôle des mesures.

-   [Résolution des problèmes de bout en bout à l’aide de métriques de stockage Azure et journalisation, AzCopy et Analyseur de Message](storage-e2e-troubleshooting.md)

    Cet article parle de dépannage à l’aide de l’espace de stockage Analytique et montre comment utiliser l’Analyseur de Message de Microsoft.

-   [Microsoft Message analyseur d’exploitation Guide](https://technet.microsoft.com/library/jj649776.aspx)

    Cet article est la référence de l’Analyseur de Message Microsoft et inclut des liens vers un didacticiel, démarrage rapide et résumé des fonctionnalités.

##<a name="cross-origin-resource-sharing-cors"></a>Ressources d’origine croisée partage (CORS)

###<a name="cross-domain-access-of-resources"></a>Accès aux autres domaines de ressources

Un navigateur web en cours d’exécution dans un domaine lorsqu’une demande HTTP pour une ressource à partir d’un autre domaine, il s’agit d’une demande HTTP origine croisée. Par exemple, une page HTML provenant de contoso.com effectue une requête pour un fichier jpeg hébergé sur fabrikam.blob.core.windows.net. Pour des raisons de sécurité, navigateurs limiter les demandes origine croisée HTTP exécutés au sein des scripts, tels que JavaScript. Cela signifie que lorsqu’un code JavaScript sur une page web sur contoso.com demande que jpeg sur fabrikam.blob.core.windows.net, le navigateur ne permettre pas la demande.

Éléments les avez à faire avec le stockage Azure ? Et bien, si vous stockez statiques actifs tels que des fichiers de données JSON ou XML dans le stockage Blob à l’aide d’un compte de stockage appelée Fabrikam, le domaine pour les biens sera fabrikam.blob.core.windows.net et l’application web contoso.com ne pourrez pas y accéder à l’aide de JavaScript, car les domaines sont différents. Ceci est également vrai si vous essayez d’appeler l’une des Services de stockage Azure, par exemple de stockage de tables – qui retournent des données JSON soient traités par le client JavaScript.

####<a name="possible-solutions"></a>Solutions possibles

Un moyen de résoudre ce problème consiste à affecter un domaine personnalisé tel que « storage.contoso.com » à fabrikam.blob.core.windows.net. Le problème est que vous pouvez uniquement affecter ce domaine personnalisé pour le compte de stockage. Que se passe-t-il si les ressources sont stockées dans plusieurs comptes de stockage ?

Autre procédure pour résoudre ce problème consiste à dispose de l’application web agir en tant que proxy pour les appels de stockage. Cela signifie que si vous téléchargez un fichier vers le stockage Blob, l’application web serait soit écrire localement et puis copiez-le dans le stockage Blob ou il serait tous ses lu en mémoire et puis écrivez-le sur le stockage d’objets Blob. Sinon, vous pouvez écrire une application web dédié (par exemple, une API Web) qui télécharge les fichiers localement et les écrit au stockage d’objets Blob. Dans les deux cas, vous devez tenir compte de la fonction lorsqu’a besoin de déterminer l’extensibilité élevées.

####<a name="how-can-cors-help"></a>Comment CORS peut vous aider ?

Stockage Azure vous permet d’activer CORS – croisées partage des ressources Origin. Pour chaque compte de stockage, vous pouvez spécifier des domaines qui peuvent accéder aux ressources dans ce compte de stockage. Par exemple, dans notre exemple ci-dessus, nous pouvons activer CORS sur le compte de stockage fabrikam.blob.core.windows.net et configurez-le pour autoriser l’accès à contoso.com. Puis le contoso.com application web peuvent accéder directement aux ressources dans fabrikam.blob.core.windows.net.

Il faut remarquer est que CORS autorise l’accès, mais elle ne propose pas d’authentification, qui est requise pour tous les autres public access des ressources de stockage. Cela signifie que vous pouvez accéder à des objets BLOB uniquement si elles sont publiques ou vous inclure une Signature accès partagés ce qui vous donne l’autorisation appropriée. Tables, files d’attente et les fichiers n’ont pas accès public et exigent une associations de sécurité.

Par défaut, CORS est désactivée sur tous les services. Vous pouvez activer CORS à l’aide de l’API REST ou la bibliothèque de stockage client pour appeler une des méthodes pour définir les stratégies de service. Lorsque vous effectuez cette opération, vous incluez une règle CORS, qui est au format XML. Voici un exemple d’une règle CORS qui a été défini à l’aide de l’opération de définir les propriétés de Service pour le Service d’objets Blob pour un compte de stockage. Vous pouvez effectuer cette opération à l’aide de la bibliothèque de stockage client ou les API REST pour le stockage Azure.

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Voici ce que signifie chaque ligne :

-   **AllowedOrigins** Cela vous indique quels domaines sans correspondance peuvent demander et recevoir des données à partir du service de stockage. Cela indique que contoso.com et fabrikam.com peuvent demander des données depuis le stockage Blob pour un compte de stockage spécifique. Vous pouvez également définir cette à un caractère générique (\*) pour permettre à tous les domaines accéder aux requêtes.

-   **AllowedMethods** Il s’agit de la liste des méthodes (verbes de requête HTTP) qui peut être utilisé lorsque vous effectuez la demande. Dans cet exemple, seules les placer et GET sont autorisées. Vous pouvez le définir sur un caractère générique (\*) pour permettre à toutes les méthodes à utiliser.

-   **AllowedHeaders** Voici les en-têtes de demande que le domaine d’origine peut spécifier en créant la demande. Dans cet exemple, tous les en-têtes de métadonnées commençant par x-ms-métadonnées, cible x-ms-meta et x-ms-meta-abc sont autorisées. Le caractère générique (\*) indique que tout début de l’en-tête par le préfixe spécifié est autorisée.

-   **ExposedHeaders** Cela vous indique les en-têtes de réponse doivent être exposés par le navigateur à l’émetteur de la demande. Dans cet exemple, un en-tête en commençant par « x-ms - meta-« sera exposée.

-   **MaxAgeInSeconds** Il s’agit de la durée maximale pendant laquelle un navigateur de cache la demande d’OPTIONS en amont. (Pour plus d’informations sur la demande de contrôle en amont, consultez l’article première ci-dessous).

####<a name="resources"></a>Ressources

Pour plus d’informations sur CORS et comment l’activer, consultez ces ressources.

-   [Prise en charge (CORS) pour les Services de stockage Azure sur Azure.com le partage des ressources origine croisée](storage-cors-support.md)

    Cet article fournit une vue d’ensemble de CORS et la manière de définir les règles pour les services de stockage différents.

-   [Prise en charge (CORS) pour les Services de stockage Azure sur MSDN le partage des ressources origine croisée](https://msdn.microsoft.com/library/azure/dn535601.aspx)

    Il s’agit de la documentation de référence pour CORS prise en charge pour les Services de stockage Azure. Il comporte des liens vers des articles appliquant à chaque service de stockage et montre un exemple et explique chaque élément dans le fichier CORS.

-   [Microsoft Azure stockage : Présentation CORS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

    Il s’agit d’un lien vers l’article de blog initial annonce CORS et montrant comment l’utiliser.

##<a name="frequently-asked-questions-about-azure-storage-security"></a>Forum aux questions sur la sécurité du stockage Azure

1.  **Comment puis-je vérifier l’intégrité des objets BLOB que je suis transfert ou disparition stockage Azure si je ne peux pas utiliser le protocole HTTPS ?**

    Si pour une raison quelconque vous devez utiliser HTTP au lieu de HTTPS et que vous travaillez avec des objets BLOB bloc, vous pouvez utiliser la vérification MD5 afin de vérifier l’intégrité des objets BLOB en cours de transfert. Cela vous aidera avec protection contre les erreurs de la couche transport/réseau, mais pas nécessairement avec les attaques intermédiaires.

    Si vous pouvez utiliser HTTPS, qui fournit une sécurité au niveau de transport, puis en utilisant la vérification MD5 est redondants et inutiles.
    
    Pour plus d’informations, consultez la [Vue d’ensemble des MD5 Azure Blob](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).

2.  **Qu’en est-il de la compatibilité FIPS pour le gouvernement des États-Unis ?**

    Les États-Unis FIPS Federal Information Processing Standard () définit les algorithmes de chiffrement approuvées par fédérales américaines systèmes informatiques pour le gouvernement pour la protection des données sensibles. L’activation FIPS mode sur un serveur de Windows ou le bureau indique le système d’exploitation qu’algorithmes de chiffrement uniquement si conformes FIPS doivent être utilisés. Si une application utilise des algorithmes irrégulière, les applications sauts de page. Versions avec.NET Framework 4.5.2 ou une version ultérieure, l’application bascule automatiquement les algorithmes de chiffrement pour utiliser les algorithmes protégées lorsque l’ordinateur est en mode FIPS.

    Microsoft laisse chaque client à décider si vous souhaitez activer le mode FIPS. Nous pensez qu’il n’existe aucune bonne raison pour les clients qui ne sont pas soumis à des réglementations gouvernementales pour activer le mode FIPS par défaut.

    **Ressources**

-   [Pourquoi nous n'allons pas recommander « Mode FIPS » plus](http://blogs.technet.com/b/secguide/archive/2014/04/07/why-we-re-not-recommending-fips-mode-anymore.aspx)

    Cet article de blog offre une vue d’ensemble de FIPS et explique pourquoi ils ne pas activer le mode FIPS par défaut.

-   [FIPS 140 Validation](https://technet.microsoft.com/library/cc750357.aspx)

    Cet article fournit des informations sur comment les produits Microsoft et les modules de chiffrement correspond à la norme FIPS pour le gouvernement fédérales américaines.

-   [« Chiffrement système : utiliser FIPS algorithmes compatibles pour le chiffrement, hachage et la signature « effets de paramètres de sécurité dans Windows XP et versions ultérieures de Windows](https://support.microsoft.com/kb/811833)

    Cet article traite de l’utilisation du mode FIPS anciens ordinateurs Windows.
