<properties
    pageTitle="FAQ - Azure Active Directory Domain Services | Microsoft Azure"
    description="Forum aux questions sur Azure Active Directory Domain Services"
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
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services : Forum aux Questions (FAQ)

Cette page fournit des réponses à des questions fréquemment posées sur Azure Active Directory Domain Services. Conserver la vérification revenir les mises à jour.

### <a name="troubleshooting-guide"></a>Guide de dépannage
Consultez notre [guide de dépannage](active-directory-ds-troubleshooting.md) pour les solutions aux problèmes courants rencontrés lors de la configuration ou l’administration des Services de domaine Active Directory Azure.


### <a name="configuration"></a>Configuration

#### <a name="can-i-create-multiple-domains-for-a-single-azure-ad-directory"></a>Puis-je créer plusieurs domaines pour un seul répertoire Azure AD ?
Non. Vous ne pouvez créer un seul domaine pris en charge par Azure Active Directory Domain Services pour un seul répertoire Azure AD.  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Puis-je activer Azure Active Directory Domain Services dans un réseau virtuel du Gestionnaire de ressources Azure ?
Non. Services de domaine Active Directory Azure ne peuvent être activés dans un réseau virtuel Azure classique. Vous pouvez vous connecter au réseau virtuel classique à un réseau virtuel du Gestionnaire de ressources à l’aide de réseau virtuel peering pour utiliser votre domaine géré dans un réseau virtuel du Gestionnaire de ressources.

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Rendre Azure Active Directory Domain Services dans plusieurs réseaux virtuels au sein de mon abonnement ?
Le service lui-même ne prend pas directement en charge ce scénario. Azure Active Directory Domain Services est disponible dans un seul réseau virtuel à la fois. Toutefois, vous pouvez configurer la connectivité entre plusieurs réseaux virtuels pour exposer des Services de domaine AD Azure à d’autres réseaux virtuel. Cet article décrit comment vous pouvez [connecter des réseaux virtuels dans Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Puis-je activer les Services de domaine Active Directory Azure à l’aide de PowerShell ?
Le déploiement automatisé/PowerShell de Services de domaine Active Directory Azure n’est pas disponible actuellement.

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>Services de domaine Active Directory Azure n’est disponible dans le nouveau portail Azure ?
Non. Services de domaine Active Directory Azure peuvent être configurés uniquement dans le [portail classique Azure](https://manage.windowsazure.com). Nous prévoyons d’étendre la prise en charge pour le [portail Azure](https://portal.azure.com) à l’avenir.

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Puis-je ajouter le domaine à un domaine géré Azure Active Directory Domain Services ?
Non. Le domaine fourni par les Services de domaine Active Directory Azure est un domaine géré. Vous n’avez pas besoin de mise en service, configurer ou gérer contrôleur de domaine pour ce domaine - ces activités de gestion sont fournies en tant que service par Microsoft. Par conséquent, vous ne pouvez pas ajouter un contrôleur (lecture seule ou en lecture seule) pour le domaine géré.

### <a name="administration-and-operations"></a>Administration et opérations

#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Puis-je me connecter au contrôleur de domaine pour mon domaine géré à l’aide de bureau à distance ?
Non. Vous ne disposez pas des autorisations pour vous connecter à contrôleur de domaine pour le domaine géré via le Bureau à distance. Les membres du groupe « Administrateurs de DC AAD » peuvent administrer le domaine géré à l’aide des outils d’administration AD tel que Active Directory Administration Centre (ADAC) ou AD PowerShell. Ces outils sont installés à l’aide de la fonctionnalité « Outils d’Administration de serveur distant » sur un serveur Windows rejoint le domaine géré.

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>J’ai activé Azure Active Directory Domain Services. Le compte d’utilisateur à utiliser sur les machines de jointure domaine pour ce domaine ?
Les utilisateurs que vous avez ajouté au groupe d’administration (par exemple, « AAD DC administrateurs ») peuvent machines jonction de domaine. En outre, les utilisateurs de ce groupe sont accordées accès Bureau à distance pour les ordinateurs qui ont été liés au domaine.

#### <a name="can-i-wield-domain-administrator-privileges-for-the-domain-provided-by-azure-ad-domain-services"></a>Puis-je exercer strict privilèges d’administrateur de domaine pour le domaine fournies par les Services de domaine Active Directory Azure ?
Non. Vous disposez pas des privilèges d’administrateur sur le domaine géré. Privilèges d’administrateur du domaine et administrateur de l’entreprise ne sont pas disponibles pour pouvoir utiliser au sein du domaine. Administrateur de domaine existant ou groupes d’administrateurs entreprise dans votre annuaire Azure AD sont également pas accordées privilèges d’administrateur d’entreprise/domaine sur le domaine.

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-domains-provided-by-azure-ad-domain-services"></a>Puis-je modifier les appartenances à un groupe à l’aide de LDAP ou autres outils d’administration AD sur les domaines fournies par les Services de domaine Active Directory Azure ?
Non. Impossible de modifier les appartenances sur les domaines pris en charge par les Services de domaine Active Directory Azure. Vaut également pour les attributs de l’utilisateur. Vous pouvez toutefois modifier appartenances ou des attributs de l’utilisateur dans Azure Active Directory ou sur votre domaine en local. Ces modifications sont automatiquement synchronisées avec Azure Active Directory Domain Services.

#### <a name="can-i-extend-the-schema-of-the-domain-provided-by-azure-ad-domain-services"></a>Puis-je étendre le schéma du domaine fourni par les Services de domaine Active Directory Azure ?
Non. Le schéma est géré par Microsoft pour le domaine géré. Extensions de schéma ne sont pas prises en charge par les Services de domaine Active Directory Azure.

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Puis-je modifier ou ajouter des enregistrements DNS de mon domaine géré ?
Oui. Utilisateurs qui figurent dans le groupe « AAD DC administrateurs » sont des privilèges ' administrateur DNS', pour modifier les enregistrements DNS dans le domaine géré. Ces utilisateurs peuvent utiliser la console DNS Manager sur un ordinateur exécutant Windows Server rejoint le domaine géré, à gérer le système DNS. Pour utiliser la console DNS Manager, installez « DNS Server Outils », qui fait partie de la fonctionnalité facultative « Outils d’Administration de serveur distant » sur le serveur. Plus d’informations sur les [utilitaires pour l’administration, surveillance et résolution des problèmes DNS](https://technet.microsoft.com/library/cc753579.aspx) sont disponibles sur TechNet.


### <a name="billing-and-availability"></a>Facturation et disponibilité

#### <a name="is-azure-ad-domain-services-a-paid-service"></a>Est qu'un service payant des Services de domaine AD Azure ?
Oui. Pour plus d’informations, consultez les [tarifs de page](https://azure.microsoft.com/pricing/details/active-directory-ds/).

#### <a name="is-there-a-free-trial-for-the-service"></a>Existe-t-il une version d’évaluation gratuite pour le service ?
Ce service est inclus dans la version d’évaluation gratuite d’Azure. Vous pouvez vous inscrire à une [version d’évaluation gratuite d’un mois d’Azure](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems"></a>Puis-je obtenir Azure Active Directory Domain Services dans le cadre d’entreprise mobilité Suite (EMS) ?
#### <a name="do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Dois-je Azure AD Premium pour utiliser les Services de domaine Active Directory Azure ?
Non. Services de domaine Active Directory Azure est un service Azure paiement à l’utilisation et ne fait pas partie d’EMS. Azure Active Directory Domain Services sont disponibles pour toutes les éditions de Azure AD (libre, de base et, Premium) et sont facturés sur une base horaire, selon l’utilisation.

#### <a name="what-azure-regions-is-the-service-available-in"></a>Quelles régions Azure est le service disponible dans ?
Reportez-vous à la page [Services Azure par région](https://azure.microsoft.com/regions/#services/) pour afficher la liste des régions Azure où Azure Active Directory Domain Services est disponible.
