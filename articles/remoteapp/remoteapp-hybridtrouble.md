
<properties
    pageTitle="Résoudre les problèmes de créer des collections de hybride RemoteApp | Microsoft Azure"
    description="Découvrez comment résoudre les problèmes de création d’une collection de sites RemoteApp hybride"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-azure-remoteapp-hybrid-collections"></a>Résoudre les problèmes de créer des collections de hybride RemoteApp Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Une collection de sites hybride est hébergé dans et stocke les données dans le cloud Azure, mais également des données d’access vous permet d’utilisateurs et des ressources que qui se trouve sur votre réseau local. Les utilisateurs peuvent accéder applications en vous connectant à l’aide de leurs informations d’identification d’entreprise synchronisé ou fédérée avec Azure Active Directory. Vous pouvez déployer une collection hybride qui utilise un réseau virtuel Azure existant, ou vous pouvez créer un nouveau réseau virtuel. Nous vous recommandons de créer ou d’utiliser un sous-réseau virtuel avec une plage CIDR assez grande pour croissance attendue pour Azure RemoteApp.

N’avez pas créé encore votre collection de sites ? Pour connaître la procédure, voir [créer une collection de sites hybride](remoteapp-create-hybrid-deployment.md) .

Si vous rencontrez des difficultés pour créer votre collection de sites, ou si la collection de sites ne fonctionne pas comme il devrait, consultez les informations suivantes.

## <a name="your-image-is-invalid"></a>Votre image n’est pas valide ##
Si vous voyez un message comme « GoldImageInvalid » lorsque vous attendez Azure mise en service de votre collection de sites, cela signifie que votre image du modèle ne répond pas à la [défini image exigences](remoteapp-imagereqs.md). Par conséquent, accédez lire ces [exigences](remoteapp-imagereqs.md), résoudre votre image, essayez de créer à nouveau votre collection de sites.



## <a name="does-your-vnet-have-network-security-groups-defined"></a>Votre VNET existe-t-il des groupes de sécurité réseau définis ? ##
Si vous avez des groupes de sécurité réseau définis sur le sous-réseau que vous utilisez pour votre collection de sites, assurez-vous que ces [URL et ports](remoteapp-ports.md) sont accessibles à partir de votre sous-réseau.

Vous pouvez ajouter des groupes de sécurité réseau supplémentaires pour les ordinateurs virtuels déployés par vous du sous-réseau pour un contrôle plus étroit.

## <a name="are-you-using-your-own-dns-servers-and-are-they-accessible-from-your-vnet-subnet"></a>Vous utilisez vos propres serveurs DNS ? Et ils sont accessibles à partir de votre sous-réseau VNET ? ##
>[AZURE.NOTE] Vous devez vérifiez que les serveurs DNS de votre VNET sont toujours vers le haut et toujours en mesure de résoudre les machines virtuelles hébergés dans le VNET. N’utilisez pas Google DNS pour cela.


Pour des collections de hybride vous utilisez vos propres serveurs DNS. Vous spécifiez les dans votre schéma de configuration du réseau ou via le portail de gestion lorsque vous créez votre réseau virtuel. Serveurs DNS sont utilisés dans l’ordre qu’ils sont spécifiés de manière basculement (contrairement à alternées).  
Reportez-vous à la [Résolution de noms pour machines virtuelles et des Instances de rôles](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) pour vous assurer que vos serveurs DNS sont configuré correcly.

Vérifiez que les serveurs DNS pour votre collection de sites sont accessibles et disponible à partir du sous-réseau VNET que vous avez spécifié pour cette collection de sites.

Par exemple :

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![Définir votre DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

## <a name="are-you-using-an-active-directory-domain-controller-in-your-collection"></a>Utilisez-vous un contrôleur de domaine Active Directory dans votre collection de sites ? ##
Actuellement seulement un domaine Active Directory peut être associé à Azure RemoteApp. La collection hybride prend en charge uniquement les comptes Azure Active Directory qui ont été synchronisés à l’aide d’outil de synchronisation d’annuaire à partir d’un déploiement Active Directory Windows Server ; plus précisément, soit synchronisées avec l’option de synchronisation de mot de passe ou synchronisé avec la fédération de Services de fédération Active Directory (AD FS) configurée. Vous devez créer un domaine personnalisé qui correspond au suffixe de domaine pour votre domaine en local et configurer l’intégration d’annuaire.

Pour plus d’informations, voir [Configuration de Active Directory pour Azure RemoteApp](remoteapp-ad.md) .

Vérifiez que les détails du domaine fournies sont valides et le contrôleur de domaine est accessible à partir de la machine virtuelle créée dans le sous-réseau utilisé pour l’application Remote Azure. Assurez-vous également que les informations d’identification du compte de service fournies disposent des autorisations pour ajouter des ordinateurs au domaine fourni et que le nom AD fourni peut être résolu à partir du DNS fourni dans la VNET.

## <a name="what-domain-name-did-you-specify-when-you-created-your-collection"></a>Quel nom de domaine avez-vous spécifié lorsque vous avez créé votre collection de sites ? ##

Le nom de domaine créé ou ajouté doit avoir un nom de domaine interne (pas votre nom de domaine Azure AD) et doit être au format DNS peut être résolu (contoso.local). Par exemple, vous avez un nom interne Active Directory (contoso.local) et un Active Directory UPN (contoso.com) : vous devez utiliser le nom interne lorsque vous créez votre collection de sites.
