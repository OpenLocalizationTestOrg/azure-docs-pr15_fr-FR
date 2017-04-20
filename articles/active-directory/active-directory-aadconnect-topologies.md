<properties
    pageTitle="Azure AD Connect : Prise en charge des topologies | Microsoft Azure"
    description="Cette rubrique décrit en détail topologies prises en charge et non prises en charge pour Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="AndKjell"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="billmath"/>

# <a name="topologies-for-azure-ad-connect"></a>Se connecter topologies pour Azure AD
L’objectif de cette rubrique est de décrire différentes en local et topologies Azure AD avec synchronisation Azure AD Connect en tant que la solution d’intégration clés. Il décrit les configurations prises en charge et non prises en charge.

Légende pour les images dans le document :

Description | Icône
-----|-----
Forêt Active Directory local| ![ACTIVE DIRECTORY](./media/active-directory-aadconnect-topologies/LegendAD1.png)
Active Directory avec importation filtrée| ![ACTIVE DIRECTORY](./media/active-directory-aadconnect-topologies/LegendAD2.png)
Serveur de synchronisation Azure AD Connect| ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync1.png)
Azure AD Connect synchronisation » mis en œuvre mode serveur »| ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync2.png)
GALSync avec FIM2010 ou MIM2016| ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync3.png)
Azure AD Connect synchronisation server, détaillé| ![Synchronisation](./media/active-directory-aadconnect-topologies/LegendSync4.png)
Azure annuaire Active Directory |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)
Scénario non prises en charge | ![Non pris en charge](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)

## <a name="single-forest-single-azure-ad-tenant"></a>Seule forêt, un seul client Azure AD
![Client unique une seule forêt](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

La plus courante topologie est une forêt unique en local, avec un ou plusieurs domaines et une annonce Azure du client. Pour l’authentification Azure AD, la synchronisation de mot de passe est utilisée. L’installation rapide de Azure AD Connect prend en charge uniquement cette topologie.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Forêt unique, plusieurs serveurs de synchronisation d’un client Azure AD
![Une seule forêt filtré non prises en charge](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

Il n’est pas pris en charge pour plusieurs serveurs de synchronisation Azure AD Connect connecté à un seul client Azure Active Directory, à l’exception d’un [serveur de test](#staging-server). Il est non prises en charge, même si celles-ci sont configurés pour synchroniser s’excluent mutuellement jeu d’objets. Vous auriez pu considérer cela si vous ne peut pas accéder à tous les domaines de la forêt à partir d’un serveur unique ou pour répartir la charge sur plusieurs serveur.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Plusieurs forêts, seul client Azure AD
![Client unique à plusieurs forêt](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

De nombreuses organisations possèdent des environnements avec plusieurs forêts Active Directory local. Il existe différentes raisons d’avoir plus d’une forêt Active Directory local. Exemples : conceptions avec forêts compte ressource et par conséquent après une fusion ou acquisition.

Lorsque vous avez plusieurs forêts, toutes les forêts doit être accessible par unique Azure AD Connect serveur de synchronisation. Vous n’êtes pas obligé de joindre le serveur à un domaine. Si nécessaire pour accéder à toutes les forêt, le serveur peut être placé dans un réseau DMZ.

L’Assistant installation Azure AD Connect offre plusieurs options pour consolider des utilisateurs représentés dans plusieurs forêts. L’objectif est qu’un utilisateur est représenté uniquement une fois dans Azure Active Directory. Il existe certaines topologies courantes que vous pouvez configurer dans le chemin d’accès d’installation personnalisée dans l’Assistant installation. Sélectionnez l’option correspondante représentant votre topologie dans la page **identifiant de vos utilisateurs**. La consolidation est configurée uniquement pour les utilisateurs. Groupes dupliqués ne sont pas consolidées avec la configuration par défaut.

Topologies courantes sont décrits dans la section suivante : [topologies distinctes](#multiple-forests-separate-topologies), [maillage complet](#multiple-forests-full-mesh-with-optional-galsync)et [Compte ressource](#multiple-forests-account-resource-forest).

La configuration par défaut dans Azure AD Connect synchroniser suppose que :

1. Les utilisateurs n'ont qu’un seul compte activé et la forêt où se trouve ce compte est utilisée pour authentifier l’utilisateur. Cette hypothèse est pour la synchronisation de mot de passe à la fois et pour la fédération. UserPrincipalName et sourceAnchor/immutableID issus de cette forêt.
2. Les utilisateurs n'ont qu’une seule boîte aux lettres.
3. La forêt qui héberge la boîte aux lettres d’un utilisateur possède la meilleure qualité de données pour les attributs visibles dans la liste d’adresses globale Exchange (LAG). S’il n’existe aucune boîte aux lettres de l’utilisateur, puis une forêt peut servir à envoyer les valeurs de ces attributs.
4. Si vous avez une boîte aux lettres lié, puis vous trouverez également un autre compte dans un autre domaine utilisé pour se connecter.

Si votre environnement ne correspond pas à ces hypothèses, les événements suivants se produisent :

- Si vous avez plusieurs comptes actif ou plusieurs boîtes aux lettres, le moteur de synchronisation sélectionne l’une et ignorer l’autre.
- Une boîte aux lettres liée avec aucun autre compte actif n’est pas exportée vers Azure Active Directory. Le compte d’utilisateur n’est pas représenté par un membre d’un groupe. Une boîte aux lettres liée dans DirSync serait toujours représenté sous la forme d’une boîte aux lettres normal. Cette modification est intentionnellement un comportement différent pour mieux gérer les scénarios forêts multiples.

Vous trouverez plus de détails à [comprendre la configuration par défaut](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Plusieurs forêts, plusieurs serveurs de synchronisation d’un client Azure AD
![Synchronisation de plusieurs à plusieurs forêt non prises en charge](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

Il n’est pas pris en charge pour avoir plus d’un serveur de la synchronisation Azure Active Directory connecter connecté à un seul client Azure AD. L’exception est l’utilisation d’un [serveur de test](#staging-server).

### <a name="multiple-forests--separate-topologies"></a>Plusieurs forêts – topologies distincts
**Les utilisateurs sont représentés qu’une seule fois sur tous les répertoires**

![Une fois les utilisateurs à plusieurs forêt](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Forêt à plusieurs séparés Topologies](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

Dans cet environnement, toutes les forêts locaux sont considérées comme des entités distinctes et aucun utilisateur ne serait présent dans n’importe quel autre forêt.
Chaque forêt possède son propre organisation Exchange et il n’y a aucune GALSync entre les forêts. Cette topologie peut être la situation après une fusion/acquisition ou dans une organisation où chaque division fonctionne isolé uns des autres. Ces forêts sont dans la même organisation dans Azure AD et apparaissent avec une liste d’adresses globale unifiée.
Dans cette image, chaque objet dans chaque forêt est représentée une seule fois dans le méta-verse et agrégé dans le client cible Azure AD.

### <a name="multiple-forests--match-users"></a>Plusieurs forêts – utilisateurs EQUIV
**Identités utilisateur existent sur plusieurs annuaires**

Pour tous ces scénarios est-il courant distribution et groupes de sécurité peuvent contenir un mélange d’utilisateurs, des contacts et FSP (principaux de sécurité étrangers)

FSP est utilisés dans ajoute pour représenter des membres à partir d’autres forêts dans un groupe de sécurité. Tous les FSP est résolus à l’objet réel dans Azure Active Directory.

### <a name="multiple-forests--full-mesh-with-optional-galsync"></a>Plusieurs forêts – maillage complet avec GALSync facultatif
**Identités utilisateur existent sur plusieurs annuaires. Faire correspondre à l’aide de : attribut de messagerie**

![Forêt à plusieurs utilisateurs courrier](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Maillage complet à plusieurs forêt](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Une topologie avec maillage complet permet aux utilisateurs et ressources se trouvent dans toute forêt et fréquemment il serait approbations bidirectionnelles entre les forêts.

Si Exchange est présent dans plus d’une forêt, vous pouvez également peuvent une solution GALSync locale. Chaque utilisateur est représenté en tant que contact dans toutes les autres forêts. GALSync est généralement implémenté à l’aide de Forefront Identity Manager 2010 ou Microsoft Identity Manager 2016. Azure AD Connect ne peuvent pas être utilisé pour GALSync en local.

Dans ce scénario, les objets d’identité sont jointes à l’aide de l’attribut mail. Un utilisateur avec une boîte aux lettres d’une forêt est joint avec les contacts dans les autres forêts.

### <a name="multiple-forests--account-resource-forest"></a>Plusieurs forêts – compte ressource forêt
**Identités utilisateur existent sur plusieurs annuaires. Faire correspondre à l’aide de : attributs ObjectSID et msExchMasterAccountSID**

![Forêt à plusieurs utilisateurs ObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![AccountResource à plusieurs forêt](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

Dans une topologie de forêt compte ressource, vous avez une ou plusieurs forêts compte avec les comptes d’utilisateurs actifs. Vous disposez également d’une ou plusieurs forêts ressource avec comptes désactivés.

Dans ce scénario une (ou plusieurs) **forêt ressource** approuve toutes les **forêts de comptes**. La forêt de ressources comporte généralement un schéma AD étendu avec Exchange et Lync. Tous les services Exchange et Lync ainsi que d’autres services partagés sont situés dans cette forêt. Les utilisateurs ont un compte d’utilisateur désactivé dans cette forêt et la boîte aux lettres est liée à la forêt du compte.

## <a name="office-365-and-topology-considerations"></a>Considérations relatives à la topologie et Office 365
Certaines charges de travail Office 365 ont certaines restrictions à topologies prises en charge. Si vous prévoyez d’utiliser un des programmes, puis lire la rubrique topologies prises en charge pour la charge de travail.

Charge de travail |  
--------- | ---------
Exchange Online | S’il existe plusieurs Exchange organisation locale (autrement dit, Exchange a été déployé sur plus d’une forêt), vous devez utiliser Exchange 2013 SP1 ou version ultérieure. Plus d’informations sont accessibles ici : [les déploiements hybrides avec plusieurs forêts Active Directory](https://technet.microsoft.com/library/jj873754.aspx)
Skype entreprise | Si vous utilisez plusieurs forêts en local, uniquement la topologie à forêt compte ressource est pris en charge. Détails des topologies prises en charge sont accessibles ici : [Configuration requise pour l’environnement pour Skype entreprise Server 2015](https://technet.microsoft.com/library/dn933910.aspx)

## <a name="staging-server"></a>Serveur intermédiaire
![Serveur intermédiaire](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect prend en charge l’installation d’un second serveur en **mode mise en attente**. Un serveur dans ce mode lit les données de tous les annuaires connectés mais n’écrit pas dans les annuaires connectés. Il utilise le cycle de synchronisation normale et par conséquent a une copie à jour les données d’identité. En cas d’urgence où le serveur principal échoue, vous pouvez basculer vers le serveur de test. Pour cela, utilisez l’Assistant Azure AD Connect. Cette deuxième serveur peut préférence résider dans un centre de données différent dans la mesure où aucune infrastructure n’est partagée avec le serveur principal. Vous devez copier manuellement les changements de configuration effectuées sur le serveur principal vers le deuxième serveur.

Un serveur intermédiaire peut également être utilisé pour tester une nouvelle configuration personnalisée et l’effet sur vos données. Vous pouvez afficher un aperçu des modifications et ajuster la configuration. Lorsque vous êtes satisfait de la nouvelle configuration, vous pouvez apporter le serveur intermédiaire le serveur actif et définir l’ancien serveur actif dans le mode de mise en attente.

Cette méthode peut également être utilisée pour remplacer le serveur de synchronisation active. Préparer le nouveau serveur et le configurer dans le mode de mise en attente. Assurez-vous qu’il est dans un état correct, désactiver les intermédiaires mode (rendant active) et arrêtez le serveur actif.

Il est possible d’avoir plusieurs serveur intermédiaire lorsque vous souhaitez effectuer des sauvegardes plusieurs centres de données différents.

## <a name="multiple-azure-ad-tenants"></a>Plusieurs clients Azure AD
Microsoft recommande de disposer d’un seul client dans Azure AD pour une organisation.
Avant de vous prévoyez d’utiliser plusieurs clients Azure Active Directory, ces rubriques traitent des scénarios courants qui vous permet d’utiliser un seul client.

Rubrique |  
--------- | ---------
Délégation de l’utilisation d’unités d’administration | [Gestion des unités d’administration dans Azure Active Directory](active-directory-administrative-units-management.md)

![Client à plusieurs à plusieurs forêt](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Il existe une relation 1:1 entre un serveur de synchronisation Azure AD Connect et un client Azure AD. Pour chaque client Azure AD, vous avez besoin d’une installation de serveur de synchronisation Azure AD Connect. Les instances de client Azure AD sont par nature isolé et dans un des utilisateurs ne peuvent pas voir les utilisateurs dans l’autre client. Si cette séparation est destinée, puis il s’agit d’une configuration prise en charge, mais dans le cas contraire, vous devez utiliser le seul modèle de client Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Chaque objet qu’une seule fois dans un client Azure AD
![Une seule forêt filtrée](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

Dans cette topologie, un serveur de synchronisation Azure AD Connect est connecté à chaque client Azure AD. Les serveurs de synchronisation Azure AD Connect doivent être configurés pour le filtrage pour chacune d’avoir un jeu d’objets pour fonctionner sur qui s’excluent mutuellement. Vous pouvez par exemple limiter chaque serveur d’un domaine particulier ou d’une unité d’organisation. Un domaine DNS peut uniquement être enregistré dans un seul client Azure AD. L’UPN des utilisateurs dans les locaux AD devez utiliser des espaces de noms distincts également. Par exemple, dans l’image ci-dessus trois UPN distincte suffixes sont enregistrés dans les locaux AD : contoso.com, fabrikam.com et wingtiptoys.com. Les utilisateurs dans chaque domaine Active Directory sur site utiliser un autre espace de noms.

Il n’y a aucune GALsync entre les instances de client Azure AD. Le carnet d’adresses dans Exchange Online et Skype pour les utilisateurs professionnels uniquement indique dans un seul client.

Cette topologie comprend les éléments suivants restrictions à autrement scénarios pris en charge :

- Les clients Azure AD seul pouvez activer Exchange hybride avec Active Directory local.
- Appareils Windows 10 peuvent uniquement être associés à un seul client Azure AD.

La configuration minimale requise pour s’excluent mutuellement jeu d’objets s’applique également pour l’écriture différée. Certaines fonctionnalités d’écriture différée ne sont pas prises en charge avec cette topologie dans la mesure où ces fonctionnalités part du principe une configuration unique local :

-   Groupe écriture différée avec configuration par défaut
-   Écriture différée appareil

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Chaque objet plusieurs fois dans un client Azure AD
![Forêt unique à plusieurs client non prises en charge](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Forêt unique à plusieurs connecteurs non prises en charge](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

- Il est non prises en charge pour synchroniser l’utilisateur même pour plusieurs clients Azure AD.
- Il est non prises en charge pour modifier une configuration pour que les utilisateurs dans une annonce Azure s’affichent sous forme de contacts dans un autre client Azure AD.
- Il est non prises en charge pour modifier la synchronisation Azure AD Connect pour vous connecter à plusieurs clients Azure AD.

### <a name="galsync-by-using-writeback"></a>GALsync à l’aide d’écriture différée
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD clients sont par nature isolé.

- Il est non prises en charge pour modifier la configuration de synchronisation Azure AD Connect pour lire les données à partir d’un autre client Azure AD.
- Il est non prises en charge pour exporter les utilisateurs en tant que contacts vers un autre local annonce à l’aide de synchronisation Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALsync avec le serveur de synchronisation local
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Il est pris en charge pour utiliser FIM2010/MIM2016 local aux utilisateurs GALsync entre les deux organisations Exchange. Les utilisateurs d’une organisation s’affiche en tant que les utilisateurs/contacts étrangères de l’autre organisation. Ces publicités locaux différents peuvent être ensuite synchronisées à leurs propres clients Azure AD.

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment installer Azure AD Connect pour ces scénarios, consultez [installation personnalisée de Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).

En savoir plus sur la configuration [Azure AD Connect synchroniser](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
