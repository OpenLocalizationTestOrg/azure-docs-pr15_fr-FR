<properties
    pageTitle="Azure Active Directory Domain Services : Instructions de mise en réseau | Microsoft Azure"
    description="Considérations relatives à la mise en réseau pour Azure Active Directory Domain Services"
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
    ms.date="10/18/2016"
    ms.author="maheshu"/>

# <a name="networking-considerations-for-azure-ad-domain-services"></a>Considérations relatives à la mise en réseau pour les Services de domaine Active Directory Azure

## <a name="how-to-select-an-azure-virtual-network"></a>Comment sélectionner un réseau virtuel Azure
Les instructions suivantes vous permettent de sélectionner un réseau virtuel à utiliser avec les Services de domaine Active Directory Azure.

### <a name="type-of-azure-virtual-network"></a>Type de réseau virtuel Azure

- Vous pouvez activer les Services de domaine Active Directory Azure dans un réseau virtuel Azure classique.

- Azure AD les Services de domaine **ne peut pas être activé dans les réseaux virtuels créés à l’aide du Gestionnaire de ressources Azure**.

- Vous pouvez vous connecter à un réseau virtuel basée sur le Gestionnaire de ressources à un réseau virtuel classique dans lequel les Services de domaine Active Directory Azure est activée. Par la suite, vous pouvez utiliser les Services de domaine Active Directory Azure dans le réseau virtuel basée sur le Gestionnaire de ressources. Pour plus d’informations, consultez la section de [la connectivité réseau](active-directory-ds-networking.md#network-connectivity) .

- **Réseaux virtuels régionaux**: Si vous prévoyez d’utiliser un réseau virtuel, assurez-vous qu’il s’agit d’un réseau virtuel régionaux.

    - Réseaux virtuels qui utilisent le mécanisme affinités héritées ne peut pas servir avec Azure Active Directory Domain Services.

    - Utiliser les Services de domaine AD Azure, [migrer héritées réseaux virtuels à des réseaux virtuels régionaux](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).


### <a name="azure-region-for-the-virtual-network"></a>Région Azure pour le réseau virtuel

- Vos Services de domaine Active Directory Azure domaine géré est déployé dans la même région Azure en tant que le réseau virtuel que vous choisissez d’activer le service dans.

- Sélectionnez un réseau virtuel dans une zone Azure pris en charge par les Services de domaine Active Directory Azure.

- Voir la page [services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans laquelle Azure Active Directory Domain Services est disponible.


### <a name="requirements-for-the-virtual-network"></a>Configuration requise pour le réseau virtuel

- **Proximité à vos charges de travail Azure**: sélectionnez le réseau virtuel qui héberge actuellement/va héberger machines virtuelles qui nécessitent un accès aux Services de domaine Active Directory Azure.

- **Serveurs DNS personnalisée/mettre vos propres**: vous assurer qu’il n’y a aucun serveur DNS personnalisé configuré pour le réseau virtuel.

- **Domaines existant portant le même nom de domaine**: Assurez-vous que vous n’avez pas un domaine existant portant le même nom de domaine disponible sur ce réseau virtuel. Par exemple, supposons que vous avez un domaine appelé « contoso.com » déjà disponibles sur le réseau virtuel sélectionné. Plus tard, vous essayez d’activer un domaine géré Azure Active Directory Domain Services avec le même nom de domaine (c'est-à-dire « contoso.com ») sur ce réseau virtuel. Vous rencontrez une erreur lorsque vous tentez d’activer les Services de domaine Active Directory Azure. Cela est dû à des conflits de noms pour le nom de domaine sur ce réseau virtuel. Dans ce cas, vous devez utiliser un autre nom pour configurer votre domaine géré Azure Active Directory Domain Services. Vous pouvez également mettre hors service le domaine existant, puis passez à activer les Services de domaine Active Directory Azure.

> [AZURE.WARNING] Vous ne pouvez pas déplacer les Services de domaine à un autre réseau virtuel une fois que vous avez activé le service.


## <a name="network-security-groups-and-subnet-design"></a>Création de groupes de sécurité et sous-réseau du réseau
Un [Groupe de sécurité réseau (NSG)](../virtual-network/virtual-networks-nsg.md) contient la liste des règles de liste de contrôle (accès) autoriser ou refuser le trafic réseau vers vos instances machine virtuelle dans un réseau virtuel. NSGs peuvent être associés aux sous-réseaux ou instances machine virtuelle individuelles dans ce sous-réseau. Lorsqu’un NSG est associé à un sous-réseau, les règles et s’appliquent à toutes les instances de ce sous-réseau machine virtuelle. En outre, le trafic vers une machine virtuelle individuel peut être restreint davantage en associant un NSG directement à cet ordinateur virtuel.

![Conception de sous-réseau recommandés](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)


### <a name="best-practices-for-choosing-a-subnet"></a>Meilleures pratiques pour le choix d’un sous-réseau
- Déploiement d’Azure Active Directory Domain Services à un **sous-réseau dédié distinct** au sein de votre réseau virtuel Azure.

- Ne s’appliquent pas NSGs au sous-réseau dédié pour votre domaine géré. Si vous devez appliquer NSGs au sous-réseau dédié, assurez-vous que vous **ne bloquent pas les ports requis du service et la gestion de votre domaine**.

- Ne pas restreindre trop le nombre d’adresses IP disponibles au sein du sous-réseau dédié pour votre domaine géré. Cette restriction empêche le service de rendre deux superflus disponibles pour votre domaine géré.

- **N’activez pas Azure Active Directory Domain Services dans le sous-réseau passerelle** de votre réseau virtuel.


> [AZURE.WARNING] Lorsque vous associez un NSG avec un sous-réseau dans lequel Azure Active Directory Domain Services est activé, vous pouvez interrompre le service et de gérer le domaine possibilité de Microsoft. En outre, la synchronisation entre votre client Azure AD et votre domaine géré est interrompue. **Le SLA ne s’applique pas aux déploiements où un NSG a été appliquée qui bloque les Services de domaine Active Directory Azure dans la mise à jour et gestion de votre domaine.**


### <a name="ports-required-for-azure-ad-domain-services"></a>Ports requis pour les Services de domaine Active Directory Azure
Les ports suivants sont requis pour les Services de domaine Active Directory Azure au service et mettre à jour votre domaine géré. Assurez-vous que ces ports ne sont pas bloqués pour le sous-réseau dans lequel vous avez activé votre domaine géré.

| Numéro de port | Objectif |
|---|---|
| 443 | Synchronisation avec votre client Azure AD |
| 3389 | Gestion de votre domaine |
| 5986 | Gestion de votre domaine |
| 636 | Sécuriser l’accès LDAP (LDAPS) à votre domaine géré |



## <a name="network-connectivity"></a>Connectivité réseau
Un domaine géré Azure Active Directory Domain Services peut être activé uniquement au sein d’un seul réseau virtuel classique dans Azure. Réseaux virtuels créés à l’aide du Gestionnaire de ressources Azure ne sont pas pris en charge.


### <a name="scenarios-for-connecting-azure-networks"></a>Scénarios pour connecter les réseaux Azure
Se connecter Azure réseaux virtuels pour utiliser le domaine géré dans un des scénarios de déploiement suivants :

#### <a name="use-the-managed-domain-in-more-than-one-azure-classic-virtual-network"></a>Utiliser le domaine géré dans plusieurs réseaux virtuel Azure classique
Vous pouvez vous connecter à d’autres réseaux virtuel classique Azure au réseau virtuel classique Azure dans lequel vous avez activé Azure Active Directory Domain Services. Cette connexion VPN vous permet d’utiliser le domaine géré avec vos charges de travail déployées dans d’autres réseaux virtuel.

![Connectivité réseau virtuel classique](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Utiliser le domaine géré dans un réseau virtuel basée sur le Gestionnaire de ressources
Vous pouvez vous connecter à un réseau virtuel basée sur le Gestionnaire de ressources au réseau virtuel classique Azure dans lequel vous avez activé Azure Active Directory Domain Services. Cette connexion vous permet d’utiliser le domaine géré avec vos charges de travail déployées dans le réseau virtuel basée sur le Gestionnaire de ressources.

![Gestionnaire de ressources pour la connectivité réseau virtuel classique](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)


### <a name="network-connection-options"></a>Options de connexion réseau

- **Connexions VNet-VNet à l’aide de connexions VPN de site à**: connexion d’un réseau virtuel à un autre réseau virtuel (VNet-VNet) est similaire à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité permet de fournir un tunnel sécurisé à l’aide de IPsec/IKE une passerelle VPN.

    ![Connectivité réseau virtuel à l’aide de passerelle VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Plus d’informations - se connecter à l’aide de la passerelle VPN des réseaux virtuels](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)


- **Connexions VNet-VNet à l’aide de réseau virtuel peering**: réseau virtuel peering est un mécanisme qui se connecte deux réseaux virtuels dans la même région via le réseau principal Azure. Une fois que ressources, les deux réseaux virtuels apparaissent sous forme de l’autre pour tous les rôles de connectivité. Ils sont toujours gérées comme des ressources distinctes, mais machines virtuelles dans ces réseaux virtuels puissent communiquer avec eux directement à l’aide des adresses IP privées.

    ![Connectivité réseau virtuel à l’aide de peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Plus d’informations - virtuels réseau peering](../virtual-network/virtual-network-peering-overview.md)



<br>

## <a name="related-content"></a>Contenu associé

- [Réseau virtuel Azure peering](../virtual-network/virtual-network-peering-overview.md)

- [Configurer une connexion VNet-VNet pour le modèle de déploiement classique](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

- [Groupes de sécurité réseau Azure](../virtual-network/virtual-networks-nsg.md)
