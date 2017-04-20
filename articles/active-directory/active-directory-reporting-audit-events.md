<properties
   pageTitle="Les événements de rapport d’audit Azure Active Directory | Microsoft Azure"
   description="Audit des événements qui sont disponibles pour l’affichage et le téléchargement à partir de votre Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/19/2016"
   ms.author="dhanyahk"/>

# <a name="azure-active-directory-audit-report-events"></a>Événements de rapport Azure Active Directory d’audit

*Cette documentation est [Azure Active Directory Reporting Guide de] (actif-répertoire-création de rapports-guide.md).*

Le rapport de vérification Azure Active Directory permet aux clients d’identifier les actions dotés de privilèges qui se sont produites dans leur Azure Active Directory. Actions dotés de privilèges incluent modifications élévation (par exemple, la création de rôle ou mot de passe), modifier les configurations de stratégie (par exemple les stratégies de mot de passe) ou les modifications apportées à la configuration de l’annuaire (par exemple, les modifications apportées aux paramètres de la fédération de domaine). Les rapports fournissent l’enregistrement d’audit pour le nom de l’événement, l’acteur ayant effectué l’action, la ressource cible affectée par la modification, la date et l’heure (UTC). Les clients sont en mesure de récupérer la liste des événements d’audit pour leur Azure Active Directory via le [Portail Azure](https://portal.azure.com/), comme décrit dans [l’affichage des journaux d’Audit](active-directory-reporting-azure-portal.md).


## <a name="list-of-audit-report-events"></a>Liste des événements de rapport d’Audit
<!--- audit event descriptions should be in the past tense --->

Événements                               | Description de l’événement
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Événements utilisateur**                      |
Ajouter un utilisateur                             | Ajouter un utilisateur à l’annuaire.
Supprimer l’utilisateur                          | Un utilisateur supprimé à partir du répertoire.
Définir les propriétés de licence               | Définir les propriétés de la licence d’un utilisateur dans l’annuaire.
Réinitialiser le mot de passe utilisateur                  | Réinitialiser le mot de passe pour un utilisateur dans l’annuaire.
Modification mot de passe utilisateur                 | Modifié le mot de passe pour un utilisateur dans l’annuaire.
Licence utilisateur modifier                  | Modifier la licence attribuée à un utilisateur dans l’annuaire. Pour savoir quelles licences ont été mis à jour, examinez les propriétés [utilisateur de mise à jour](#update-user-attributes) ci-dessous
Mettre à jour utilisateur                          | Mise à jour un utilisateur dans l’annuaire. [Voir ci-dessous](#update-user-attributes) pour les attributs pouvant être mis à jour.
Mot de passe utilisateur jeu force modifier       | Définissez la propriété qui force la création d’un utilisateur à modifier leur mot de passe sur connexion.
Mettre à jour les informations d’identification utilisateur        | Utilisateur a modifié le mot de passe  
**Événements de groupe**                     |
Ajouter un groupe                            | Créer un groupe dans l’annuaire.
Groupe de mise à jour                         | Mise à jour d’un groupe dans l’annuaire. Pour afficher les propriétés du groupe ont été mis à jour, reportez-vous au [Groupe audit de propriétés](#update-group-attributes) dans la section ci-dessous
Supprimer le groupe                         | Supprimer un groupe à partir du répertoire.
Ajouter des membres au groupe            | Ajouter un membre à un groupe dans l’annuaire.
Supprimer un membre du groupe         | Supprimé un membre d’un groupe dans l’annuaire.
CreateGroupSettings              | Paramètres de groupe créé
UpdateGroupSettings          | Mises à jour les paramètres de groupe. Pour afficher les paramètres de groupe ont été mis à jour, reportez-vous au [Groupe audit de propriétés](#update-group-attributes) dans la section ci-dessous
DeleteGroupSettings            | Paramètres de groupe supprimés
SetGroupLicense                  | Définition de licence de groupe.
SetGroupManagedBy              | Définir le groupe devant être gérés par utilisateur
AddGroupMember               | Membre ajouté au groupe
RemoveGroupMember            | Supprimer un membre du groupe
AddGroupOwner                | Propriétaire ajouté au groupe
RemoveGroupOwner                 | Supprimé propriétaire du groupe
**Événements d’application**               |
Ajouter principal du service                | Ajouter un principal de service à l’annuaire.
Supprimer l’entité de service             | Supprimer un principal de service à partir du répertoire.
Ajouter des informations d’identification principal de service    | Informations d’identification ajoutées à une entité de service.
Supprimer les informations d’identification principal de service | Supprimer les informations d’identification d’un service principal.
Ajouter entrée de délégation                 | Créé un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) dans le répertoire.
Entrée de délégation ensemble                 | Mise à jour un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) dans l’annuaire.
Supprimer l’entrée de délégation              | Supprimer un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) dans l’annuaire.
**Événements de rôle**                      |
Ajouter un membre de rôle au rôle              | Ajouter un utilisateur à un rôle d’annuaire.
Supprimer un membre de rôle du rôle         | Supprimé un utilisateur d’un rôle d’annuaire.
Définir les informations de contact      | Définir les préférences de contact au niveau de l’entreprise. Cela inclut les adresses de messagerie pour les notifications de marketing, ainsi que les techniques à propos de Microsoft Online Services.
Ajouter entrée de délégation                 | Créé un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) dans le répertoire.
Entrée de délégation ensemble                 | Mise à jour un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) dans l’annuaire.
Supprimer l’entrée de délégation              | Supprimer un [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) dans l’annuaire.
AddSevicePrincipalOwner          | Propriétaire ajouté à principal du service.
RemoveSevicePrincipalOwner   | Supprimé propriétaire de principal du service.
AddApplication  | Ajouter une application.
UpdateApplication | Mettre à jour d’application. Pour savoir quels paramètres de l’application ont été mis à jour, reportez-vous à [Audit des propriétés d’Application](#update-application-attributes) dans la section ci-dessous
DeleteApplication | Supprimer l’application.
RestoreApplication|Restauration d’application.
AddApplicationOwner   |     Ajoutez propriétaire de l’application.
RemoveApplicationOwner    |     Supprimer le propriétaire d’application.
**Événements de rôle**                         |
Ajouter un membre de rôle au rôle                 | Ajouter un utilisateur à un rôle d’annuaire.
Supprimer un membre de rôle du rôle            | Supprimé un utilisateur d’un rôle d’annuaire.
AddRoleDefinition               | Définition de rôle ajouté.
UpdateRoleDefinition                | Mise à jour la définition de rôle. Pour afficher les paramètres de rôle ont été mis à jour, reportez-vous à [Auditer de rôle définition de propriétés](#update-role-definition-attributes) dans la section ci-dessous
DeleteRoleDefinition                | Définition de rôle supprimé.
AddRoleAssignmentToRoleDefinition | Attribution de rôle ajouté à définition de rôle.
RemoveRoleAssignmentFromRoleDefinition | Attribution de rôle supprimé à partir de la définition de rôle.
AddRoleFromTemplate     | Rôle ajouté à partir du modèle.
UpdateRole  | Rôle mis à jour.
AddRoleScopeMemberToRole    | Membre dans l’étendue ajouté au rôle.
RemoveRoleScopedMemberFromRole  | Supprimé dans l’étendue membre du rôle.
**APPAREIL événements (tous les nouveaux événements)**                    |
Ajout de périphérique               | Périphérique ajouté.
UpdateDevice            | Périphérique mis à jour. Pour afficher les propriétés de l’appareil ont été mis à jour, reportez-vous aux [Propriétés d’un périphérique contrôlé](#update-device-attributes) dans la section ci-dessous
DeleteDevice            | APPAREIL supprimé.
AddDeviceConfiguration      | Configuration du périphérique ajouté.
UpdateDeviceConfiguration   | Configuration du périphérique mis à jour. Pour afficher les propriétés de configuration appareil ont été mis à jour, reportez-vous aux [Propriétés de configuration de périphérique contrôlé](#update-device-configuration-attributes) dans la section ci-dessous
DeleteDeviceConfiguration   | Configuration du périphérique supprimés.
AddRegisteredOwner     | Propriétaire enregistré ajouté au périphérique.
AddRegisteredUsers     | Utilisateurs inscrits ajoutés au périphérique.
RemoveRegisteredOwner    | Supprimer le propriétaire enregistré à partir de l’appareil.
RemoveRegisteredUsers    | Supprimer des utilisateurs inscrits d’appareil.
RemoveDeviceCredentials  | Supprimer les informations d’identification de l’appareil.
**Événements B2B**                       |
Invitations lot téléchargement.              | Un administrateur a téléchargé un fichier contenant des invitations à envoyer aux utilisateurs partenaire.
Invitations par lots traitées.             | Un fichier contenant des invitations aux utilisateurs partenaire a été traité.
Inviter des utilisateurs externes.                | Un utilisateur externe a été invité à l’annuaire.
Échanger inviter des utilisateurs externes.         | Un utilisateur externe a échangé leur invitation à l’annuaire.
Ajouter des utilisateurs externes au groupe.          | Un utilisateur externe a été affecté l’appartenance à un groupe dans l’annuaire.
Assigner des utilisateurs externes à application. | Accès direct a été affecté à un utilisateur externe, à l’application.
Création de virus client.               | Un nouveau client a été créé dans Azure AD par le remboursement d’invitation.
Création de l’utilisateur de virus.                 | Un utilisateur a été créé dans un client existant dans Azure Active Directory par le remboursement d’invitation.
**Unités administratives (tous les nouveaux événements)**             |
AddAdministrativeUnit   |   Ajouter l’unité d’administration.
UpdateAdministrativeUnit|   Unité d’administration de la mise à jour. Pour afficher les propriétés d’une unité d’administration ont été mis à jour, reportez-vous à [Auditer des propriétés d’une unité d’administration](#update-administrative-unit-attributes) dans la section ci-dessous
DeleteAdministrativeUnit|   Supprimer une unité d’administration.
AddMemberToAdministrativeUnit|  Ajouter des membres à unité d’administration.
RemoveMemberFromAdministrativeUnit|     Supprimer des membres d’unité d’administration.
**Événements d’annuaire**                 |
Ajouter un partenaire à société               | Ajouter un partenaire à l’annuaire.
Supprimer un partenaire de l’entreprise          | Supprimer un partenaire à partir du répertoire.
DemotePartner   |   Abaisser partenaire.
Ajouter un domaine à la société                | Ajouter un domaine à l’annuaire.
Supprimer le domaine d’entreprise           | Supprimer un domaine à partir du répertoire.
Mettre à jour domaine                        | Mise à jour d’un domaine dans l’annuaire. Pour afficher les propriétés du domaine ont été mis à jour, reportez-vous à [Auditer des propriétés du domaine](#update-domain-attributes) dans la section ci-dessous
Définir l’authentification de domaine            | Modifié le paramètre de domaine par défaut pour la société.
Définir les informations de contact      | Définir les préférences de contact au niveau de l’entreprise. Cela inclut les adresses de messagerie pour les notifications de marketing, ainsi que les techniques à propos de Microsoft Online Services.
Définir les paramètres de la fédération de domaine    | Mises à jour les paramètres de la fédération pour un domaine.
Vérifier le domaine                        | Vérifier un domaine dans l’annuaire.
Vérifier le domaine vérifié de messagerie         | Vérifier un domaine sur le répertoire à l’aide de la vérification de messagerie.
Définir l’indicateur DirSyncEnabled sur société   | Définissez la propriété qui permet un répertoire pour la synchronisation Azure Active Directory.
Définir la stratégie de mot de passe                  | Définir des contraintes de longueur et les caractères pour les mots de passe utilisateur.
Informations sur la société de jeu              | Mises à jour les informations au niveau de la société. Consultez l’applet de commande PowerShell [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) pour plus d’informations.
SetCompanyAllowedDataLocation  |    Jeu de société autorisée emplacement des données.
SetCompanyDirSyncEnabled    |   Définissez DirSyncEnabled indicateur.
SetCompanyDirSyncFeature |  Définir la fonctionnalité de synchronisation d’annuaire.
SetCompanyInformation |   Informations sur la société ensemble.
SetCompanyMultiNationalEnabled    |     Définir la fonctionnalité multinationales société est activée.
SetDirectoryFeatureOnTenant   |     Définir la fonctionnalité répertoire client.
SetTenantLicenseProperties  |   Définir les propriétés de la licence client.
CreateCompanySettings   |   Créer des paramètres de la société
UpdateCompanySettings   |   Mettre à jour les paramètres de la société. Pour afficher les propriétés de la société ont été mises à jour, reportez-vous à [Auditer des propriétés de la société](#update-company-attributes) dans la section ci-dessous
DeleteCompanySettings   |   Supprimer les paramètres de la société
SetAccidentalDeletionThreshold   |  Définir le seuil de suppression accidentelle.
SetRightsManagementProperties   |   Définir les propriétés de la gestion des droits.
PurgeRightsManagementProperties     |   Effacer les propriétés de gestion des droits.
UpdateExternalSecrets   |   Mettre à jour les secrets externes
**Événements de stratégie (tous les nouveaux événements)**           |
AddPolicy   |   Ajouter une stratégie.
UpdatePolicy    |   Mettre à jour la stratégie.
DeletePolicy    |   Supprimer la stratégie.
AddDefaultPolicyApplication     |   Ajouter la stratégie à l’application.
AddDefaultPolicyServicePrincipal    |   Ajouter la stratégie à principal du service.
RemoveDefaultPolicyApplication  |   Supprimer la stratégie d’application.
RemoveDefaultPolicyServicePrincipal     |   Supprimer la stratégie de principal du service.
RemovePolicyCredentials     |   Supprimer les informations d’identification de la stratégie.

## <a name="audit-report-retention"></a>Rétention de rapport d’audit
Événements dans le rapport d’Audit Azure AD sont conservées pendant 180 jours. Pour plus d’informations sur la rétention sur les rapports, voir [Stratégies de rétention de rapport Azure Active Directory](active-directory-reporting-retention.md).

Pour les clients d’informations sur le stockage de leurs événements d’audit pour les périodes de rétention, l’API de création de rapports peut servir à extraire régulièrement des événements d’audit dans un magasin de données distinct. Pour plus d’informations, voir [Prise en main avec l’API de création de rapports](active-directory-reporting-api-getting-started.md) .

## <a name="properties-included-with-each-audit-event"></a>Propriétés incluses dans chaque événement d’audit

Propriété      | Description
------------- | --------------------------------------------------------------
Date et heure | La date et l’heure qui s’est produite lors de l’événement d’audit
Acteur         | L’utilisateur ou le principal de service qui a effectué l’action
Action        | L’action qui a été exécutée
Cible        | L’utilisateur ou le principal du service l’action exécutée sur


## <a name="update-user-attributes"></a>Attributs « Mettre à jour utilisateur »
L’événement d’audit « Utilisateur mise à jour » inclut des informations supplémentaires sur les attributs utilisateur ont été mis à jour. Pour chaque attribut, la valeur précédente et la nouvelle valeur est inclus.

Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled                      | L’utilisateur peut se connecter.
AssignedLicense                     | Toutes les licences qui ont été affectées à l’utilisateur.
AssignedPlan                        | Offres de service qui résulte de licences attribuées à l’utilisateur.
LicenseAssignmentDetail             | Plus d’informations sur les licences attribuées à l’utilisateur. Par exemple, si basée sur un groupe de licences a été impliquer, cela inclut le groupe auquel la licence accordée.
Mobile                              | Téléphone mobile de l’utilisateur.
OtherMail                           | Adresse de messagerie alternative de l’utilisateur.
OtherMobile                         | Téléphone mobile secondaire de l’utilisateur.
StrongAuthenticationMethod          | Liste des méthodes de vérification configuré par l’utilisateur pour l’authentification multifacteur, par exemple appel vocal, SMS ou vérification du code à partir d’une application mobile.
StrongAuthenticationRequirement     | Si l’authentification multifacteur est appliquée, activé ou désactivé pour cet utilisateur.
StrongAuthenticationUserDetails     | Numéro de téléphone de l’utilisateur, autre téléphone numéro et l’adresse e-mail utilisée pour la vérification de réinitialiser l’authentification multifacteur et mot de passe.
StrongAuthenticationPhoneAppDetail  | Applications du téléphone sont forof détail enregistré pour effectuer l’ouverture de session 2FA
Numéro de téléphone                     | Numéro de téléphone de l’utilisateur.
AlternativeSecurityId               | Un code de sécurité alternative pour l’objet.
CreationType                        |Méthode de création de l’utilisateur (soit par invitation ou virus).
InviteTicket                        |Liste de tickets invitation pour l’utilisateur.
InviteReplyUrl                      |Liste d’URL pour répondre à l’acceptation d’invitation.
InviteResources                     |Liste des ressources à laquelle l’utilisateur a été invité.
LastDirSyncTime                     |Dernière mise à l’objet en raison de la synchronisation à partir de la faisant autorité répertoire (client, localement).
MSExchRemoteRecipientType           |Cartes de types de destinataires MSO. Reportez-vous à https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx [types de destinataires MSO] pour les types de destinataires
PreferredDataLocation               |L’emplacement souhaité pour l’utilisateur, du groupe, du contact, dossiers publics, ou données de l’appareil.
ProxyAddresses                      |L’adresse par lequel un objet destinataire Exchange Server est reconnu dans un système de messagerie étranger.
StsRefreshTokensValidFrom           |Ballon actualise les informations de révocation jeton - des jetons actualisation STS délivrés avant que cette fois sont considérés comme expiré.
UserPrincipalName                   |Le nom UPN est un nom de connexion Internet-style d’un utilisateur.
UserState                           |État de l’utilisateur approbation en cours/PendingAcceptance/accepté/PendingVerification.
UserStateChangedOn                  |Horodatage de dernière modification apportée à UserState. Utilisé pour déclencher la lecture du cycle de vie des flux de travail.
UserType                            |Type d’utilisateur. Membre de (0), invité (1), Viral(2).

## <a name="update-group-attributes"></a>Attributs de « Groupe mise à jour »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Classification            | Le classement d’un groupe unifiée (IEA, IMA, etc.).
Description               | Phrases descriptives explicite sur l’objet.  
Nom complet               |Le nom d’affichage pour un objet
DirSyncEnabled            |Indique si la synchronisation se produit à partir d’une autorité répertoire (client, localement).
GroupLicenseAssignment    |Affectation de licence d’un groupe
GroupType                 |Type de groupe, unifiée (0)
IsMembershipRuleLocked    |Indique que la propriété MembershipRule est définie par le service de gestion libre-service du groupe et ne peut pas être modifiée par les utilisateurs. S’applique uniquement aux groupes où GroupType comporte GroupType.DynamicMembership
IsPublic                  |Indicateur pour indiquer si le groupe est public/privé.
LastDirSyncTime           |Dernière mise à l’objet à la suite de synchronisation à partir de la faisant autorité répertoire (client, local).
Courrier                      |Adresse de messagerie principale
MailEnabled               |Indique si ce groupe possède la fonction de courrier électronique.
MailNickname              |Nom d’un objet de carnet d’adresses, généralement la partie de son nom de messagerie précédent la "@" symbole.   
MembershipRule            |Chaîne exprimant les critères utilisés par le service de gestion de groupe libre-service pour déterminer quels membres doivent appartenir à ce groupe. Voir aussi IsMembershipRuleLocked. S’applique uniquement aux groupes où GroupType comporte GroupType.DynamicMembership.
MembershipRuleProcessingState| Valeur d’énumération définie par le service de gestion de groupe libre-service définir le statut d’appartenance de ce groupe de traitement. S’applique uniquement aux groupes où GroupType comporte GroupType.DynamicMembership.
ProxyAddresses            |L’adresse par lequel un objet destinataire Exchange Server est reconnu dans un système de messagerie étranger.
RenewedDateTime           |Enregistrement d’horodatage des lorsqu’un groupe a été récemment renouvelé.   
SecurityEnabled           |Indique si l’appartenance dans le groupe peut-être influencer les décisions d’autorisation.
WellKnownObject           |Présente un objet annuaire, désigner comme partie d’un ensemble prédéfini.

## <a name="update-device-attributes"></a>Attributs « Mettre à jour de périphérique »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | Indique si une entité de sécurité peut s’authentifier.
CloudAccountEnabled | Indique si une entité de sécurité peut s’authentifier. Rédigé par InTune lorsque l’appareil est maîtriser local.
CloudDeviceOSType | Type de l’appareil basé sur le système d’exploitation Windows RT, iOS, par exemple. Lorsque la valeur par un service cloud (par exemple, Intune), cet attribut devient faisant autorité dans l’annuaire pour DeviceOSType.
CloudDeviceOSVersion | Version du système d’exploitation. Lorsque la valeur par un service cloud (par exemple, Intune), cet attribut devient faisant autorité dans l’annuaire pour DeviceOSVersion.
CloudDisplayName | Valeur de l’attribut LDAP displayName. Lorsque la valeur par un service cloud (par exemple, Intune), cet attribut devient faisant autorité dans l’annuaire pour nom complet.
CloudCreated |Indique si l’objet a été créé par les services en nuage.
CompliantUntil | Jusqu'à quelle heure appareil est considéré comme conforme.
DeviceMetadata |Métadonnées personnalisées pour l’appareil
DeviceObjectVersion | Cet attribut est utilisé pour identifier la version du schéma de l’appareil.
DeviceOSType |Type de l’appareil basé sur le système d’exploitation Windows RT, iOS, par exemple. Rédigé par le Service d’enregistrement et destinés à être mis à jour par les périphériques mobiles de service de gestion ou STS lumière service de gestion des.
DeviceOSVersion |Version du système d’exploitation. Rédigé par le Service d’enregistrement et destinés à être mis à jour par les périphériques mobiles de service de gestion ou STS lumière service de gestion des.
DevicePhysicalIds |Attribut à valeurs multiples conçues pour stocker les identificateurs de l’appareil physique. Il peut s’agir BIOS ID, empreintes plateforme sécurisée, matériel ID spécifiques, etc..
DirSyncEnabled | Indique si la synchronisation se produit à partir d’un faisant autorité (client, local) répertoire.
Nom complet |Le nom d’affichage pour un objet
IsCompliant | Cet attribut est utilisé pour gérer l’état de gestion des périphériques mobiles du périphérique.
IsManaged |Cet attribut est utilisé pour indiquer que l’appareil est géré par un nuage MDM.
LastDirSyncTime |Dernière mise à l’objet en raison de la synchronisation à partir de la faisant autorité répertoire (client, local).

## <a name="update-device-configuration-attributes"></a>Attributs « Mettre à jour de la Configuration du périphérique »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
MaximumRegistrationInactivityPeriod |Le nombre maximal de jours pendant lesquels qu'un appareil peut être inactif avant qu’il est considéré comme pour la suppression.
RegistrationQuota   |Stratégie permet de limiter le nombre d’enregistrements appareil autorisées pour un seul utilisateur.

## <a name="update-service-principal-configuration-attributes"></a>Attributs « Mettre à jour de la Configuration du Service principale »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled |Indique si une entité de sécurité peut s’authentifier.
AppPrincipalId | Identité externes, définis par l’application pour une entité de sécurité.
Nom complet |Le nom d’affichage pour un objet
Nom principal de service    | Un nom principal de service, contenant « nom/autorité » où nom indique une valeur de classe application et autorité contienne au moins hostname [ : port] ou « nom » qui spécifie un identificateur pour l’entité de service.

## <a name="update-app-attributes"></a>Attributs « Mettre à jour d’application »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |Le jeu d’adresses (rediriger URL) affectés à un principal de service.
Identificateur                               |ID de l’application de l’application   
AppIdentifierUri | Application URI, qui identifie l’application.  Il s’agit généralement de l’URL d’application access.
AppLogoUrl |L’url de l’image du logo application stockée dans un CDN.
AvailableToOtherTenants | True l’application est client multiples (c'est-à-dire peuvent être utilisées par d’autres clients).
Nom complet | Le nom d’affichage pour un nom d’Application
Droit |Liste des droits de l’application.
ExternalUserAccountDelegationsAllowed |Indicateur indiquant si application ressource est approuvé et peut créer des entrées de délégation pour les comptes d’utilisateurs externes.
GroupMembershipClaims |L’appartenance aux groupes de revendications stratégie.
PublicClient | True si le client ne peut pas ne pas divulguer (c'est-à-dire non confidentielle client dans OAuth2.0)
RecordConsentConditions | Types de conditions consentement, telle que définie par les termes du contrat : aucun (0), SilentConsentForPartnerManagedApp(1). Cette valeur s’affichera dans le schéma API Graph et ne peut définir/modifiés par les administrateurs client.
RequiredResourceAccess |Contenu XML d’une valeur de la propriété RequiredResourceAccess.
WebApp |Si true, indique que cette application est une application web.
WwwHomepage |La page Web principale.

## <a name="update-role-attributes"></a>Attributs « Mettre à jour de rôle »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |Le jeu d’adresses (rediriger URL) affectés à un principal de service.
BelongsToFirstLoginObjectSet |Si vrai, indique que cet objet appartient à l’ensemble des objets requis pour activer la connexion de l’administrateur première d’un nouveau client.
Intégrés |Indique si la durée de vie d’un objet appartient par le système.
Description | Phrases descriptives explicite sur l’objet.  
Nom complet |Le nom d’affichage pour un objet
MailNickname | Nom d’un objet de carnet d’adresses, généralement la partie de son nom de messagerie précédent la "@" symbole.
RoleDisabled |Indique si le rôle doit être ignoré à des fins de contrôle d’accès.
RoleTemplateId | Identité du modèle de rôle.
ServiceInfo |Service de mise à disponibilité informations spécifiques pouvant être utilisés par MOAC et/ou autres instances de service (ou plusieurs type de service).
TaskSetScopeReference |Identifie une tâcheDéfinir et un ensemble d’étendues associés à un rôle ou RoleTemplate.
ValidationError |Informations publiées par un service fédéré décrivant une erreur non transitoires et spécifiques au service en ce qui concerne les propriétés ou le lien à partir d’une action de l’administrateur objet à résoudre.
WellKnownObject |Présente un objet annuaire, désigner comme partie d’un ensemble prédéfini.

## <a name="update-role-definition-attributes"></a>Attributs « Mettre à jour la définition de rôle »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AssignableScopes    |Collection des portées d’autorisations qui peuvent être référencées lorsque vous affectez cet RoleDefinition à une entité de sécurité.
Nom complet     |Le nom d’affichage pour un objet
GrantedPermissions  |Autorisations accordées par cette RoleDefinition.


## <a name="update-administrative-unit-attributes"></a>Attributs « Mettre à jour d’unité Administrative »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Description |   Cette propriété est mis à jour lorsque vous modifiez la description d’une unité d’administration.
Nom complet |   Cette propriété est mis à jour lorsque vous modifiez le nom d’une unité d’administration.

## <a name="update-company-attributes"></a>Attributs de « Société mise à jour »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AllowedDataLocation     | Un emplacement dans lequel les utilisateurs de la société sont autorisés à mettre en service.
AuthorizedServiceInstance   | Noms des instances de service dans lequel un plan peut être déployé.
DirSyncEnabled |Indique si la synchronisation se produit à partir d’un faisant autorité (client, local) répertoire.
DirSyncStatus |Indique si la synchronisation des objets de carnet d’adresses dans ce contexte client se produit à partir d’un faisant autorité (client, local) répertoire ; une extension de la propriété DirSyncEnabled sur objets de la société.
DirSyncFeatures |Indicateur binaire pour suivre ensemble de fonctionnalités dirsync activé et désactivé pour le client.
DirectoryFeatures |Fonctions directory activé ou désactivé.
DirSyncConfiguration |Contient tous les DirSync configuration spécifiques pour le client en cours.
Nom complet |Le nom d’affichage pour un objet
IsMnc |Un indicateur booléen défini sur « true » données que l’entreprise est activé pour la fonctionnalité de société multinationales.
ObjectSettings | Un ensemble de paramètres applicables à la portée de l’objet.
PartnerCommerceUrl |URL du site du partenaire commerce.
PartnerHelpUrl |Adresse URL du site de l’aide du partenaire.
PartnerSupportEmail | URL de messagerie de prise en charge du partenaire.
PartnerSupportTelephone |URL de téléphone du support technique du partenaire.
PartnerSupportUrl |Adresse URL du site de prise en charge du partenaire.
StrongAuthenticationDetails |Détails relatifs aux StrongAuthentication.
StrongAuthenticationPolicy |Stratégie d’authentification renforcée pour la société.
TechnicalNotificationMail |Adresse de messagerie pour être averti des problèmes techniques relatifs à la société.
Numéro de téléphone |Numéros de téléphone respectez les E.123 recommandation ITU.
TenantType |Le type d’un client. Si cette valeur n’est pas spécifiée, le client est une société. Dans le cas contraire, les valeurs possibles sont : MicrosoftSupport (0), SyndicatePartner (1), (2) BreadthPartner BreadthPartnerDelegatedAdmin (3) ResellerPartnerDelegatedAdmin (4) ValueAddedResellerPartnerDelegatedAdmin (5).
VerifiedDomain |Un ensemble de noms de domaine DNS lié à une société.

## <a name="update-domain-attributes"></a>Attributs « Mettre à jour de domaine »
Attribut                           | Description
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
Fonctionnalités    |Bits indicateurs décrivant les fonctionnalités du domaine, le cas échéant.
Par défaut |Indique si le domaine est la valeur par défaut ; par exemple, le suffixe par défaut UserPrincipalName lorsqu’un administrateur crée un nouvel utilisateur dans MOAC.
Initiale |Indique si le domaine est le domaine initial de la société, comme attribué par OCP. Le domaine initial est un sous-domaine unique d’un domaine Microsoft Online ; e.g.contoso3.microsoftonline.com.
LiveType    |Type de Windows Live espace de noms correspondant, le cas échéant.
Nom    | Identificateur pour le point de terminaison.
PasswordNotificationWindowDays  |Le nombre de jours avant l’expiration un mot de passe de l’utilisateur reçoit une notification.
PasswordValidityPeriodDays  | Le nombre de jours qu'un mot de passe est préférable pour avant qu’elle doit être modifiée.

Les enregistrements d’audit sont un contrôle requis pour les nombreuses réglementations. Pour les clients utilisent le rapport de d’Audit annuaire Active Azure pour répondre à leurs réglementations, il est recommandé que le client envoyer une copie de cette rubrique d’aide avec la copie du rapport d’audit exporté du client pour vous aider à comprendre les détails du rapport. Si l’auditeur aimerions comprendre les respect des réglementations répondant aux Azure actuellement, demander à l’auditeur à la [page de conformité](https://azure.microsoft.com/support/trust-center/compliance/) de la Microsoft Azure Trust Center.
