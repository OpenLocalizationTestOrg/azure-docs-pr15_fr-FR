<properties
    pageTitle="Azure Active Directory hybride identité conception - définir la stratégie de protection des données | Microsoft Azure"
    description="Vous devez définir la stratégie de protection des données de votre solution identité hybride répondre aux besoins de l’entreprise que vous avez défini."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Définir la stratégie de protection des données de votre solution identité hybride

Dans cette tâche, vous devez définir la stratégie de protection des données de votre solution identité hybride répondre aux besoins de l’entreprise que vous avez défini dans :

- [Déterminer les besoins de protection des données](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
- [Déterminer les besoins de gestion de contenu](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
- [Déterminer les exigences de contrôle d’accès](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
- [Déterminer les besoins de réponse aux incidents](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Définir les options de protection des données
Comme il a été indiqué dans les [exigences de synchronisation d’annuaire déterminer](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), Microsoft Azure AD peut être synchronisés avec vos Services de domaine Active Directory (AD DS) situé en local. Cette intégration permet aux organisations d’exploiter Azure AD pour vérifier les informations d’identification utilisateur quand ils essayez d’accéder à des ressources d’entreprise. Vous pouvez faire pour les deux scénarios : données au reste en local et dans le cloud.  Accès aux données dans Azure AD nécessite l’authentification des utilisateurs via un service de jeton de sécurité (STS).

Une fois authentifié, le nom d’utilisateur principal (UPN) est lu à partir du jeton d’authentification et la partition répliquée et conteneur correspondant au domaine de l’utilisateur est déterminé. Informations sur l’existence de l’utilisateur, état activé et rôle sont utilisées par le système d’autorisation pour déterminer si l’accès demandé pour le client cible est autorisé pour cet utilisateur dans cette session. Certaines actions autorisées (en particulier, créer un utilisateur, mot de passe réinitialisé) créer un journal d’audit qui peut être utilisé par un administrateur client pour gérer les efforts de conformité ou d’enquêtes.

Déplacement des données à partir de votre centre de données locales dans le stockage Azure via une connexion Internet ne peuvent pas toujours être réalisables en raison de volume de données, la disponibilité de bande passante ou autres considérations relatives à. Le [Service de stockage sur Importer/exporter Azure](../storage/storage-import-export-service.md) propose une option matérielle pour placer/récupération de grands volumes de données dans le stockage blob. Il vous permet d’envoyer [chiffrées BitLocker](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) disques durs directement à un centre de données Azure où opérateurs cloud sont-elles immédiatement le contenu à votre compte de stockage, ou ils peuvent télécharger vos données Azure à vos lecteurs pour revenir à vous. Uniquement les disques chiffrés sont acceptées pour ce processus (en utilisant une clé BitLocker générée par le service lui-même pendant l’installation de tâche). La clé BitLocker est fournie à Azure séparément, ce qui assure se déconnecter de partage de clé bande.

Dans la mesure où les données lors des transferts peuvent avoir lieu dans différents scénarios, est également utile de savoir que Microsoft Azure utilise [un réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) pour isoler le trafic des clients à partir d’une autre, utilisant des mesures tels que pare-feux au niveau hôte et invité filtrage des paquets, blocage de ports et les points de terminaison HTTPS. Toutefois, la plupart des communications internes d’Azure, y compris l’infrastructure à l’infrastructure et infrastructure client (local), également chiffrée. Un autre scénario importantes est les communications au sein des centres de données Azure ; Microsoft gère les réseaux pour vous assurer qu’aucune machine virtuelle peut imiter espionner sur l’adresse IP d’un autre. SSL/TLS est utilisé lorsque vous accédez à stockage Azure ou les bases de données SQL, ou lorsque vous vous connectez aux Services Cloud. Dans ce cas, l’administrateur client est responsable de l’obtention d’un certificat SSL/TLS et son déploiement sur leur infrastructure client. Données du trafic déplacement entre Machines virtuelles dans le même déploiement ou entre les clients dans un seul déploiement via Microsoft Azure réseau virtuel peut être protégée via les protocoles de communication chiffrée comme HTTPS, SSL/TLS ou d’autres personnes.

Selon la façon dont vous avez répondu aux questions des [besoins de protection des données déterminer](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), vous devez être en mesure de déterminer comment vous voulez protéger vos données et la façon dont la solution d’identité hybride vous aidera sur ce. Le tableau indique les options prises en charge par Azure disponibles pour chaque scénario de protection des données.


| Options de protection des données         | Au reste dans le cloud | Au reste en local | Lors des transferts |
|---------------------------------|----------------------|---------------------|------------|
| Chiffrement de lecteur BitLocker      | X                    | X                   |            |
| SQL Server pour chiffrer les bases de données | X                    | X                   |            |
| Machine virtuelle-virtuelle chiffrement             |                      |                     | X          |
| SSL/TLS                         |                      |                     | X          |
| VPN                             |                      |                     | X          |


>[AZURE.NOTE]
Lisez [la conformité par fonctionnalité](https://azure.microsoft.com/support/trust-center/services/) au [Centre de gestion de la confidentialité de Microsoft Azure](https://azure.microsoft.com/support/trust-center/) en savoir plus sur la certification chaque service Azure est compatible avec.
Étant donné que les options de protection des données utilisent une approche multicouche, comparaison entre ces options ne sont pas applicables pour cette tâche. Vous assurer que vous tirez parti toutes les options disponibles pour chaque état dans lequel les données seront.

## <a name="define-content-management-options"></a>Définir les options de gestion de contenu
L’un des avantages de l’utilisation d’Azure AD pour gérer une infrastructure d’identité hybride sont que le processus est entièrement transparent du point de vue de l’utilisateur final. L’utilisateur essaiera d’accéder à une ressource partagée, la ressource requiert une authentification, l’utilisateur a envoyer une demande d’authentification à Azure AD afin d’obtenir le jeton et accéder à la ressource. Ce processus se produit en arrière-plan, sans interaction de l’utilisateur. Il est également possible accorder l’autorisation à un [groupe](active-directory-manage-groups.md#getting-started-with-access-management) d’utilisateurs afin de lui permettre d’effectuer certaines actions courantes.

Organisations qui sont généralement préoccuper de confidentialité des données nécessitent la classification des données pour leur solution. Si leur infrastructure locale actuelle se sert déjà de classification de données, il est possible d’exploiter Azure AD comme référentiel principal pour l’identité de l’utilisateur. Un outil commun qu’il est utilisé en local pour la classification des données est appelé Windows Server 2012 R2 [Boîte à outils de Classification de données](https://msdn.microsoft.com/library/Hh204743.aspx) . Cet outil peut vous aider à identifier, classer et protéger les données sur les serveurs de fichiers dans votre cloud privé. Il est également possible d’exploiter la [Classification automatique des fichiers](https://technet.microsoft.com/library/hh831672.aspx) dans Windows Server 2012 pour effectuer cette opération.

Si votre organisation n’a pas la classification des données en place, mais a besoin pour protéger des fichiers sensibles sans ajouter de nouveaux serveurs en local, ils peuvent utiliser Microsoft [Azure Rights Management Service](https://technet.microsoft.com/library/JJ585026.aspx).  Azure RMS utilise le chiffrement, d’identité et de stratégies d’autorisation pour sécuriser vos fichiers et la messagerie électronique et qu’il fonctionne sur plusieurs appareils — téléphones et tablettes PC. Azure RMS étant un service cloud, il est inutile de configurer explicitement approbations avec d’autres organisations avant que vous pouvez partager du contenu protégé avec eux. Si elles ont déjà un Office 365 ou un répertoire Azure AD, collaboration entre organisations est automatiquement pris en charge. Vous pouvez également synchroniser uniquement les attributs de répertoire Azure RMS doit prendre en charge d’une identité courantes pour vos comptes Active Directory local, à l’aide de Azure Active Directory Synchronization Services (AAD Sync) ou Azure AD Connect.

Une partie essentielle de gestion de contenu à comprendre qui accède à la ressource, par conséquent, une fonctionnalité de journalisation enrichi est importante pour la solution de gestion des identités. Azure AD fournit journal plus de 30 jours, y compris :

- Modifications apportées à l’appartenance aux rôles (ex : utilisateur ajouté au rôle Administrateur général)
- Mises à jour des informations d’identification (ex : modification du mot de passe)
- Gestion des domaines (ex : vérifier un domaine personnalisé, la suppression d’un domaine)
- Ajout ou suppression d’applications
- Gestion des utilisateurs (ex : ajout, suppression, mise à jour d’un utilisateur)
- Ajout ou suppression de licences

>[AZURE.NOTE]
Lisez [Microsoft Azure sécurité et gestion du journal d’Audit](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) pour en savoir plus sur les fonctionnalités de journalisation dans Azure.
Selon la façon dont vous avez répondu aux questions [en matière de gestion de contenu de déterminer](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md), vous devez être en mesure de déterminer comment vous souhaitez que le contenu à gérer dans votre solution identité hybride. Si toutes les options présentées dans le tableau 6 sont capables de l’intégration de Azure AD, il est important de définir quel est le plus adapté à vos besoins professionnels.

| Options de gestion de contenu                                                               | Avantages                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Inconvénients liés                                                                                                                                                                                                                               |
|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Centralisé locaux (Active Directory Rights Management Server)                      | Contrôle total sur l’infrastructure du serveur responsable de la classification des données <br> Fonctionnalité intégrée dans Windows Server, pas besoin d’une licence supplémentaire ou d’abonnement <br> Peut être intégré aux Azure AD dans un scénario hybride <br> Prend en charge des fonctionnalités de gestion des (information IRM) de droits relatifs à l’information dans Microsoft Online services tels que Exchange Online et SharePoint Online, ainsi que Office 365 <br> Prend en charge des produits serveur Microsoft en local, tels que les serveurs de fichiers qui exécutent Windows Server et Infrastructure de Classification de fichier (FCI), Exchange Server et SharePoint Server. | Une version ultérieure, maintenance (paragraphes solidaires des mises à jour, la configuration et mises à niveau potentielles), depuis informatique possède le serveur <br> Exiger une infrastructure server en local<br> Doesn'tleverage fonctionnalités Azure en mode natif                                     |
| Centralisation dans le cloud (Azure RMS)                                                     | Plus facile à gérer par rapport à la solution en local <br> Peut être intégré à AD DS dans un scénario hybride <br>  Totalement intégré à Azure AD <br> Ne nécessite pas un serveur local pour déployer le service <br> Prend en charge en local produits serveur de Microsoft tels que les serveurs de fichiers qui exécutent Windows Server et Classification des fichiers, Infrastructure (FCI), serveur, Exchange Server et SharePoint <br> Service informatique, pouvez contrôlez clé leur locataire avec fonctionnalité BYOK.                                                                                    | Votre organisation peut comporter qu’un abonnement cloud qui prend en charge RMS <br> Votre organisation doit avoir un répertoire Azure AD pour prendre en charge l’authentification des utilisateurs pour RMS                                                                                  |
| Hybride (Azure RMS intégré à, en local Active Directory Rights Management Server) | Ce scénario sont additionnés avantages à la fois, centralisée en local et dans le cloud.                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Votre organisation peut comporter qu’un abonnement cloud qui prend en charge RMS <br> Votre organisation doit avoir un répertoire Azure AD pour prendre en charge l’authentification des utilisateurs pour RMS, <br> Nécessite une connexion entre le service cloud Azure et infrastructure local |

## <a name="define-access-control-options"></a>Définir les options de contrôle d’accès
Grâce à l’authentification, l’autorisation et contrôle d’accès fonctionnalités disponibles dans Azure AD vous ne pourrez pas permettre à votre entreprise à utiliser un référentiel central identité tout en permettant aux utilisateurs et partenaires à utiliser authentification unique (SSO) comme indiqué dans l’illustration ci-dessous :

![](./media/hybrid-id-design-considerations/centralized-management.png)

Gestion centralisée et entièrement l’intégration avec d’autres annuaires

Azure Active Directory fournit une authentification unique à des milliers d’applications SaaS et applications web en local. Veuillez lire la [liste de compatibilité Azure Active Directory federation : fournisseurs d’identité tiers peuvent être utilisés pour implémenter l’authentification unique](https://msdn.microsoft.com/library/azure/jj679342.aspx) article pour plus d’informations sur l’authentification unique tiers qui ont été testés par Microsoft. Cette fonctionnalité permet de mettre en œuvre une variété de scénarios B2B tout en conservant le contrôle de la gestion des identités et access. Toutefois, pendant la B2B le processus de conception est importante de comprendre la méthode d’authentification qui sera utilisée par le partenaire et valide si cette méthode est pris en charge par Azure. Il s’agit actuellement des méthodes prises en charge par Azure Active Directory :

- Sécurité Assertion Markup Language (SAML)
- OAuth
- Kerberos
- Jetons
- Certificats


>[AZURE.NOTE] lire les [Protocoles Azure Active Directory d’authentification](https://msdn.microsoft.com/library/azure/dn151124.aspx) pour en savoir plus sur chaque protocole et ses fonctionnalités dans Azure.

À l’aide de la prise en charge Azure AD, applications d’entreprise mobile permet l’expérience d’authentification de Services mobiles facile même pour permettre aux employés pour vous connecter à leurs applications mobiles avec leurs informations d’identification Active Directory d’entreprise. Cette fonctionnalité Azure AD est pris en charge en tant que fournisseur d’identité dans les Services mobiles en parallèle avec les autres fournisseurs d’identité que déjà prennent en charge (qui incluent Accounts Microsoft, ID de Facebook, ID de Google et Twitter ID). Si les applications en local utilise les informations d’identification de l’utilisateur située à AD DS la société, l’accès à partir de partenaires et les utilisateurs bientôt à partir du cloud doit être transparent. Vous pouvez gérer le contrôle d’accès conditionnel de l’utilisateur aux applications web (basées sur le cloud), API web, services cloud Microsoft, 3e partie SaaS applications et les applications clientes (mobile) natifs et que les avantages de la sécurité, l’audit, création de rapports au sein d’un seul endroit. Toutefois, il est recommandé pour valider ce dans un environnement de test ou avec un nombre limité d’utilisateurs.


>[AZURE.TIP] Il est important de préciser qu’Azure AD ne comporte pas de stratégie de groupe comme AD DS a. Pour appliquer une stratégie pour appareils mobiles vous avez besoin d’une solution de gestion des périphériques mobiles tels que [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).

Une fois que l’utilisateur s’authentifie via AD Azure, il est important d’évaluer le niveau d’accès que l’utilisateur a il. Le niveau d’accès que l’utilisateur a sur une ressource peut varier, tandis que Azure AD peut ajouter un niveau de sécurité supplémentaire en contrôlant l’accès à certaines ressources, vous devez également n’oubliez pas que la ressource elle-même peut également avoir son propre liste de contrôle d’accès séparément, tels que le contrôle d’accès pour les fichiers situés dans un serveur de fichiers. L’illustration suivante résume les différents niveaux de contrôle d’accès que vous pouvez avoir dans un scénario hybride :

![](./media/hybrid-id-design-considerations/accesscontrol.png)


Chaque interaction dans le diagramme a montré dans l’illustration X représente un scénario de contrôle d’accès qui peut être couverts par Azure AD. Ci-dessous, vous avez une description de chaque scénario :

1.Cliquez conditionnel accès aux applications qui sont hébergés en local : vous pouvez utiliser les appareils inscrits avec les stratégies d’accès pour les applications qui sont configurées pour utiliser AD FS avec Windows Server 2012 R2. Pour plus d’informations sur la configuration des accès conditionnel pour local, voir [comment configurer l’accès conditionnelle locales à l’aide d’Azure Active Directory d’inscription pour appareils](active-directory-conditional-access-on-premises-setup.md).
2.Cliquez contrôle d’accès pour portail de gestion Azure : Azure a également la possibilité de contrôler l’accès au portail de gestion à l’aide de RBAC (rôle en fonction de contrôle d’accès). Cette méthode permet de la société limiter la durée d’opérations une personne peut faire une fois qu’il a accès au portail de gestion Azure. À l’aide de RBAC pour contrôler l’accès au portail, autorité de certification les administrateurs informatiques déléguer l’accès à l’aide de l’accès gestion des approches suivantes :

 - Attribution de rôle basée sur le groupe : vous pouvez affecter l’accès aux groupes d’annonces Azure qui peuvent être synchronisés à partir de votre Active Directory local. Cela vous permet d’exploiter les investissements de votre organisation a été dans les outils et les processus de gestion des groupes. Vous pouvez également utiliser la fonctionnalité de gestion de groupe déléguée d’Azure AD Premium.
 - Exploiter intégré rôles dans Azure : vous pouvez utiliser des trois rôles : propriétaire, collaboration et Reader, pour vous assurer que les utilisateurs et groupes êtes autorisé à effectuer uniquement les tâches dont elles ont besoin pour accomplir leurs tâches.
 - Accès précis aux ressources : vous pouvez attribuer des rôles aux utilisateurs et groupes pour un abonnement spécifique, groupe de ressources ou une ressource individuelle Azure tel qu’un site Web ou une base de données. De cette façon, vous pouvez vous assurer que les utilisateurs ont accès à toutes les ressources que dont elles ont besoin et aucun accès aux ressources qu’ils n’avez pas besoin de gérer.

>[AZURE.NOTE] [contrôle d’accès basé sur un rôle dans Azure](https://azure.microsoft.com/updates/role-based-access-control-in-azure-preview-portal/) pour en savoir plus sur cette fonctionnalité en lecture. Pour les développeurs qui créent des applications et want personnaliser le contrôle d’accès pour eux, il est également possible d’utiliser des rôles d’Application Azure AD pour l’autorisation. Passez en revue cet [exemple WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) relatifs à la création de votre application pour utiliser cette fonctionnalité.

3 accès conditionnel pour les applications Office 365 avec Microsoft Intune : les administrateurs informatiques peuvent prévoir les stratégies d’appareil accès conditionnelle pour sécuriser les ressources d’entreprise, tout en autorisant travailleurs de l’information sur les appareils compatibles pour accéder aux services. Pour plus d’informations, voir [Conditionnelle appareil les stratégies d’accès pour les services Office 365](active-directory-conditional-access-device-policies.md).

4.la conditionnelle accès pour les applications Saas : [cette fonctionnalité](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) permet de vous permettent de configurer des règles d’accès de l’authentification multifacteur par application et la possibilité de bloquer l’accès des utilisateurs se trouvent pas sur un réseau approuvé. Vous pouvez appliquer les règles de l’authentification multifacteur à tous les utilisateurs affectés à l’application, ou uniquement pour les utilisateurs au sein de groupes de sécurité spécifiés. Les utilisateurs peuvent être exclues l’exigence de l’authentification multifacteur si ils accèdent à l’application à partir d’une adresse IP dans à l’intérieur de l’organisation du réseau.

Étant donné que les options de contrôle d’accès utilisent une approche multicouche, comparaison entre ces options ne sont pas applicables pour cette tâche. Vous assurer que vous tirez parti toutes les options disponibles pour chaque scénario qui vous oblige à contrôler l’accès à vos ressources.

## <a name="define-incident-response-options"></a>Définir les options de réponse aux incidents
Azure AD peut aider IT et identité potentielle risques dans l’environnement par analyse de l’activité de l’utilisateur, service informatique peut exploitent Azure AD Access et les rapports d’utilisation fonctionnalité visibilité sur l’intégrité référentielle et la sécurité de l’annuaire de votre organisation. Grâce à ces informations, un administrateur informatique peut mieux déterminer où les risques de sécurité est peut-être dû afin qu’ils puissent correctement à limiter ces risques.  [Abonnement Azure AD Premium](active-directory-get-started-premium.md) dispose d’un ensemble de rapports de sécurité peuvent activer informatique pour obtenir ces informations. [Rapports AD Azure](active-directory-view-access-usage-reports.md) sont classés comme indiqué ci-dessous :

- **Rapports anomalie**: contiennent les événements que nous avons trouvé être anormale de connexion. Notre objectif est de prendre une telle activité et vous permettent d’être en mesure de déterminer si un événement est suspect.
- **État de l’Application intégrée**: fournit des informations sur l’utilisation des applications en nuage de votre organisation. Azure Active Directory offre l’intégration avec des milliers d’applications cloud.
- **Rapports d’erreurs**: indiquent les erreurs pouvant se produire lors de la configuration des comptes à des applications externes.
- **Rapports spécifiques à l’utilisateur**: afficher appareil/dans les données de l’activité d’un utilisateur spécifique.
- **Journaux d’activité**: contiennent un enregistrement de tous les événements vérifiés dans les dernières 24 heures, 7 derniers jours, ou 30 derniers jours, ainsi que les changements d’activité groupe et activité réinitialiser et d’enregistrement de mot de passe.

>[AZURE.TIP]
Un autre rapport qui peut également aider l’équipe de réponse aux incidents travaillant sur un cas est l’état de [l’utilisateur avec des informations d’identification perdues](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) .  Ce rapport couvre les correspondances trouvées entre ces liste informations d’identification perdue et votre client.

Autres importantes intégré dans les rapports Azure AD qui peut être utilisée pendant une enquête incidents et sont :

- **Activité de réinitialisation du mot de passe**: indiquez l’administrateur avec des aperçus dans comment activement réinitialisation du mot de passe est utilisée dans l’organisation.
- **Mot de passe réinitialisé l’activité d’enregistrement**: fournit des informations dans lequel les utilisateurs ont enregistrées leurs méthodes pour réinitialiser votre mot de passe, et les méthodes sélectionné.
- **Activité de groupe**: fournit un historique des modifications au groupe (ex : utilisateurs ajoutés ou supprimés) qui ont été ouvertes dans le panneau d’accès.

Outre la fonctionnalité de création de rapports de base disponible dans Azure AD Premium qui peuvent être exploitées lors d’un processus d’enquête de réponse aux incidents, service informatique peut également tirer parti de ressources rapport d’Audit pour obtenir des informations telles que :

- Modifications apportées à l’appartenance aux rôles (ex : utilisateur ajouté au rôle Administrateur général)
- Mises à jour des informations d’identification (ex : modification du mot de passe)
- Gestion des domaines (ex : vérifier un domaine personnalisé, la suppression d’un domaine)
- Ajout ou suppression d’applications
- Gestion des utilisateurs (ex : ajout, suppression, mise à jour d’un utilisateur)
- Ajout ou suppression de licences

Étant donné que les options de réponse aux incidents utilisent une approche multicouche, comparaison entre ces options ne sont pas applicables pour cette tâche. Vous assurer que vous tirez parti toutes les options disponibles pour chaque scénario que vous devez utiliser la fonctionnalité de création de rapports AD Azure dans le cadre du processus de réponse aux incidents de votre entreprise.

## <a name="next-steps"></a>Étapes suivantes
[Déterminer les tâches de gestion des identités hybride](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)


## <a name="see-also"></a>Voir aussi
[Vue d’ensemble des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)
