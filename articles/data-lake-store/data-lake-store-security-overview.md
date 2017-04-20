<properties
   pageTitle="Vue d’ensemble de la sécurité dans le magasin de Lake données | Microsoft Azure"
   description="Comprendre comment Azure Data Lake Store est un magasin de données volumineux plus sécurisé"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# <a name="security-in-azure-data-lake-store"></a>Sécurité Azure données Lake Store

De nombreuses entreprises Tirez parti d’analytique de données volumineux pour avoir un aperçu entreprise pour les aider à prendre des décisions intelligentes. Une organisation peut avoir un environnement complexe et réglementé, avec un nombre d’utilisateurs divers croissant. Il est essentiel pour une entreprise pour vous assurer que les données critiques sont stockées en toute sécurité, avec le niveau d’accès accordé à des utilisateurs individuels correct. Magasin de Lake données Azure est conçu pour aider à répondre à ces exigences de sécurité. Dans cet article, en savoir plus sur les fonctionnalités de sécurité Lake de magasin de données, notamment :

* Authentification
* Autorisation
* Isolement réseau
* Protection des données
* L’audit

## <a name="authentication-and-identity-management"></a>Gestion d’identité et d’authentification

L’authentification est le processus par lequel l’identité d’un utilisateur est vérifiée lorsque l’utilisateur interagit avec données Lake banque ou avec n’importe quel service qui se connecte au magasin des données Lake. Pour l’authentification et la gestion des identités, données Lake banque utilise [Azure Active Directory](../active-directory/active-directory-whatis.md), une identité complète et accès gestion cloud solution qui simplifie la gestion des utilisateurs et groupes.

Chaque abonnement Azure peut être associé à une instance d’Azure Active Directory. Seuls les utilisateurs et des identités de service qui sont définies dans votre service d’Azure Active Directory peuvent accéder à votre compte Data Lake Store, à l’aide du portail Azure, outils de ligne de commande, ou par le biais des applications clientes votre organisation crée à l’aide du SDK Azure Data Lake Store. Principaux avantages de l’utilisation d’Azure Active Directory comme un mécanisme de contrôle d’accès centralisé sont :

* Gestion du cycle de vie des identités simplifiée. L’identité d’un utilisateur ou un service (une identité principale du service) peut être rapidement créée et révoquée rapidement en supprimant simplement ou la désactivation du compte dans l’annuaire.

* Authentification multifacteur. [L’authentification multifacteur](../multi-factor-authentication/multi-factor-authentication.md) fournit un niveau de sécurité pour les compléments d’authentification utilisateur et transactions supplémentaire.

* Authentification à partir de n’importe quel client via un protocole ouvert standard, par exemple OAuth ou OpenID.

* Fédération avec les services d’annuaire de l’entreprise et fournisseurs d’identité cloud.

## <a name="authorization-and-access-control"></a>Contrôle d’accès et d’autorisation

Après que Azure Active Directory authentifie un utilisateur afin que l’utilisateur peut accéder Azure données Lake Store, contrôles d’autorisation d’accéder aux autorisations de données Lake Store. Données Lake Store sépare d’autorisation pour les activités lié à un compte et liées aux données de la manière suivante :

* [Contrôle d’accès basé sur un rôle](../active-directory/role-based-access-control-what-is.md) (RBAC) fournie par Azure pour la gestion des comptes
* ACL POSIX pour accéder aux données dans le magasin


### <a name="rbac-for-account-management"></a>RBAC pour la gestion des comptes

Quatre rôles base sont définis pour données Lake Store par défaut. Les rôles autorisent plusieurs opérations d’un compte de données Lake Store via le portail Azure, applets de commande PowerShell et des API REST. Les rôles propriétaire et collaborateur peuvent effectuer un éventail de fonctions d’administration sur le compte. Vous pouvez affecter le rôle de lecteur aux utilisateurs qui n’interagissent avec des données.

![Rôles RBAC] (./media/data-lake-store-security-overview/rbac-roles.png "Rôles RBAC")

Notez que bien que les rôles sont affectés pour la gestion des comptes, certains rôles affectent l’accès aux données. Vous devez utiliser des utilisateurs pour contrôler l’accès aux opérations qu’un utilisateur peut effectuer sur le système de fichiers. Le tableau suivant présente un résumé des droits de gestion des droits d’accès de données pour les rôles par défaut.

| Rôles                    | Droits de gestion               | Droits d’accès aux données | Explication |
| ------------------------ | ------------------------------- | ------------------ | ----------- |
| Aucun rôle affecté         | Aucun                            | Régi par et    | L’utilisateur ne peut pas utiliser le portail Azure ou les applets de commande PowerShell Azure pour parcourir le magasin Lake de données. L’utilisateur peut utiliser les outils de ligne de commande uniquement.
| Propriétaire  | Tous les  | Tous les  | Le rôle de propriétaire est un super utilisateur. Ce rôle peut gérer tous les éléments et comporte un accès intégral aux données.
| Reader   | En lecture seule  | Régi par et    | Le rôle de lecteur peut afficher tous les éléments en matière de gestion de compte, telles que les utilisateurs est affectée à quel rôle. Le rôle de lecteur ne peut pas apporter des modifications.   |
| Collaboration              | Tout sauf l’ajouter et supprimer des rôles | Régi par et    | Le rôle de collaborateur peut gérer certains aspects d’un compte, tels que les déploiements et la création et la gestion des alertes. Le rôle de collaborateur ne peut pas ajouter ou supprimer des rôles.
| Administrateur de l’accès utilisateur | Ajouter et supprimer des rôles            | Régi par et    | Le rôle d’administrateur de l’accès utilisateur peut gérer l’accès aux comptes d’utilisateur. |

Pour plus d’informations, voir [affecter des utilisateurs ou groupes de sécurité pour les comptes de données Lake Store](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>À l’aide des utilisateurs pour les opérations sur les systèmes de fichiers

Données Lake Store est un système de fichiers hiérarchiques comme Hadoop Distributed fichier système (HDFS) et prise en charge des [Utilisateurs POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Contrôles de lecture (r), écriture (w) et exécuter des (autorisations à des ressources pour le rôle de propriétaire, pour le groupe de propriétaires et d’autres utilisateurs et groupes x). Dans le Lake Store Public aperçu des données (la version actuelle), les utilisateurs sont activés sur le dossier racine, sous-dossiers et des fichiers individuels. Les utilisateurs que vous appliquez au dossier racine s’appliquent également à tous les dossiers et fichiers enfants.

Nous vous recommandons de définir des utilisateurs pour plusieurs utilisateurs à l’aide de [groupes de sécurité](../active-directory/active-directory-accessmanagement-manage-groups.md). Ajouter des utilisateurs à un groupe de sécurité, puis attribuer ensuite les utilisateurs pour un fichier ou dossier à ce groupe de sécurité. Ceci est utile lorsque vous souhaitez fournir un accès personnalisé, car vous n’êtes limité à l’ajout d’un maximum de neuf entrées pour l’accès personnalisé. Pour plus d’informations sur la façon de mieux sécuriser les données stockées dans le magasin Lake de données à l’aide de groupes de sécurité Azure Active Directory, voir [Attribution d’utilisateurs ou groupe de sécurité comme utilisateurs au système de fichiers Azure données Lake Store](data-lake-store-secure-data.md#filepermissions).

![Accès à la liste standard et personnalisé] (./media/data-lake-store-security-overview/adl.acl.2.png "Accès à la liste standard et personnalisé")

## <a name="network-isolation"></a>Isolement réseau

Utiliser le magasin Lake données afin de contrôler l’accès à votre magasin de données au niveau du réseau. Vous pouvez établir pare-feux et définir une plage d’adresses IP pour vos clients approuvés. Une plage d’adresses IP, seuls les clients qui ont une adresse IP dans la plage définie peuvent se connecter aux données Lake Store.

![Paramètres de pare-feu et accès IP] (./media/data-lake-store-security-overview/firewall-ip-access.png "Paramètres du pare-feu et l’adresse IP")

## <a name="data-protection"></a>Protection des données

Organisations voulez vous assurer que leurs données critiques soient sécurisées tout au long de son cycle de vie. Pour les données lors des transferts, données Lake Store utilise le protocole de sécurité TLS (Transport Layer) normalisé pour sécuriser les données qui se déplacement entre un client et données Lake Store.

Protection des données pour les données inactives seront disponible dans les versions ultérieures.

## <a name="auditing-and-diagnostic-logs"></a>Journaux d’audit et de Diagnostics

Vous pouvez utiliser les journaux d’audit ou de diagnostics, selon que vous recherchez des journaux des activités de gestion ou des activités liées aux données.

*  Activités de gestion utilisent Azure Gestionnaire de ressources API et utilisées dans le portail Azure via les journaux d’audit.
*  Activités liées aux données utilisent WebHDFS reste API et utilisées dans le portail Azure via les journaux de diagnostic.

### <a name="auditing-logs"></a>Journaux d’audit

Pour respecter les réglementations, une organisation peut nécessiter des journaux d’audit adéquate s’il faut attentivement incidents spécifiques. Données Lake Store a intégrés de surveillance et d’audit et enregistre toutes les activités de gestion de compte.

Compte gestion des journaux d’audit, afficher et choisissez les colonnes que vous souhaitez ouvrir une session. Vous pouvez également exporter des journaux d’audit au stockage Azure.

![Journaux d’audit] (./media/data-lake-store-security-overview/audit-logs.png "Journaux d’audit")

### <a name="diagnostic-logs"></a>Journaux de diagnostic

Vous pouvez définir des journaux d’audit de l’accès aux données dans le portail Azure (dans les paramètres de Diagnostic) et créer un compte de stockage Blob Azure où se trouvent les journaux.

![Journaux de diagnostic] (./media/data-lake-store-security-overview/diagnostic-logs.png "Journaux de diagnostic")

Après avoir configuré les paramètres de diagnostic, vous pouvez afficher les journaux sous l’onglet **Journaux de Diagnostic** .

Pour plus d’informations sur l’utilisation des journaux de diagnostic avec Azure données Lake banque d’informations, voir [les journaux de diagnostic d’accès de données Lake Store](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Résumé

Les clients d’entreprise exigent une plateforme en nuage données analytique qui est sécurisé et facile à utiliser. Magasin de Lake données Azure est conçu pour aider face que ces exigences via la gestion des identités et l’authentification via l’intégration Azure Active Directory, d’autorisations et, isolement réseau, chiffrement des données de transfert et placez (bientôt à l’avenir) et l’audit.

Si vous voulez voir nouvelles fonctionnalités de données Lake Store, envoyez-nous vos commentaires sur le [forum de données Lake Store UserVoice](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble Azure Lake de magasin de données](data-lake-store-overview.md)
- [Prise en main données Lake Store](data-lake-store-get-started-portal.md)
- [Sécuriser les données dans les données Lake Store](data-lake-store-secure-data.md)
