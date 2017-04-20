<properties
   pageTitle="Configurer un réseau virtuel et la passerelle pour ExpressRoute dans le portail classique | Microsoft Azure"
   description="Cet article décrit la configuration d’un réseau virtuel pour ExpressRoute à l’aide du portail classique et le modèle de déploiement classique."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/20/2016"
   ms.author="cherylmc"/>

# <a name="create-a-virtual-network-for-expressroute-in-the-classic-portal"></a>Créer un réseau virtuel pour ExpressRoute dans le portail classique

Les étapes décrites dans cet article vous guidera dans la configuration d’un réseau virtuel et une passerelle réseau virtuel pour une utilisation avec ExpressRoute à l’aide du portail classique et le modèle de déploiement classique.

Si vous recherchez des instructions pour le modèle de déploiement du Gestionnaire de ressources, vous pouvez utiliser les articles suivants : [créer un réseau virtuel à l’aide de PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) et [Ajouter une passerelle VPN vers un VNet Gestionnaire de ressources pour ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="create-a-classic-vnet-and-gateway"></a>Créer une passerelle et un VNet classique

Les étapes suivantes créent un VNet classique et une passerelle réseau virtuel. Si vous disposez déjà d’un VNet classique, consultez la section [configurer un VNet classique existant](#config) dans cet article.

1. Connectez-vous au [portail classique Azure](http://manage.windowsazure.com).

2. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services de réseau**, puis cliquez sur **Réseau virtuel**. Cliquez sur **Créer personnalisée** pour lancer l’Assistant de configuration.

3. Dans la page **Détails du réseau virtuel** , entrez les informations suivantes :

    - **Nom** : nom de votre réseau virtuel. Vous allez utiliser ce nom de réseau virtuel lorsque vous déployez votre instances machines virtuelles et PaaS, et vous préférerez peut-être pas pour que le nom plus compliqué.
    - **Emplacement** – l’emplacement est directement lié à l’emplacement physique (région) où vous souhaitez vos ressources (machines virtuelles) réside. Par exemple, si vous souhaitez que les ordinateurs virtuels que vous déployez sur ce réseau virtuel doit être physiquement situé dans le fuseau horaire, sélectionnez cet emplacement. Vous ne pouvez pas modifier la zone associée à votre réseau virtuel après sa création.

4. Dans la page **DNS Servers et connectivité VPN** , entrez les informations suivantes, puis cliquez sur la flèche suivant dans le coin inférieur droit. 

    - **Serveurs DNS** - Entrez le nom du serveur DNS et l’adresse IP, ou sélectionnez un serveur DNS précédemment enregistré dans le menu contextuel. Ce paramètre ne crée pas un serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de nom pour ce réseau virtuel.
    - **Connectivité de Site à** : activez la case à cocher pour **configurer un réseau privé virtuel de site à**.
    - **ExpressRoute** – activez la case à cocher **Utiliser ExpressRoute**. Cette option s’affiche uniquement si vous avez sélectionné **un réseau privé virtuel de Site à configurer**.
    - **Réseau local** , vous devez disposer d’un site de réseau local pour ExpressRoute. Toutefois, dans le cas d’une connexion ExpressRoute, les préfixes d’adresse spécifiés pour le site de réseau local sont ignorées. En revanche, les préfixes d’adresse publiés vers Microsoft par le biais du circuit ExpressRoute seront utilisées pour le routage.<BR>Si vous avez déjà un réseau local créé pour votre connexion ExpressRoute, vous pouvez le sélectionner dans la liste déroulante. Dans le cas contraire, sélectionnez **spécifier un réseau Local**.

5. La page de **Site à connectivité** s’affiche si vous avez sélectionné pour spécifier un réseau local à l’étape précédente. Pour configurer votre réseau local, entrez les informations suivantes, puis sur la flèche suivant. 

    - **Nom** : le nom que vous voulez appeler votre local (local) réseau site.
    - **Espace d’adressage** - notamment IP de début et CIDR (nombre d’adresses). Vous pouvez spécifier une plage d’adresses dans la mesure où elle ne chevauche pas la plage d’adresses pour votre réseau virtuel. En règle générale, ce serait spécifier les plages d’adresses pour vos réseaux en local, mais dans le cas ExpressRoute, ces paramètres ne sont pas utilisés. Toutefois, ce paramètre est requis afin de créer le réseau local lorsque vous utilisez le portail classique.
    - **Ajouter un espace adresse** : ce paramètre n’est pas pertinent pour ExpressRoute.


6. Dans la page **Des espaces d’adressage réseau virtuel** , entrez les informations suivantes, puis sur la case à cocher dans le coin inférieur droit pour configurer votre réseau. 

    - **Espace d’adressage** - y compris le démarrage Nb IP et l’adresse. Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas un des espaces d’adressage installée sur votre réseau local.
    - **Ajouter sous-réseau** - en incluant des IP de début et le nombre d’adresses. Sous-réseaux supplémentaires ne sont pas obligatoires.
    - **Sous-réseau de passerelle ajouter** - cliquez pour ajouter le sous-réseau passerelle. Le sous-réseau passerelle est utilisé uniquement pour la passerelle réseau virtuel et est nécessaire pour cette configuration.<BR>Le sous-réseau passerelle CIDR (nombre d’adresses) pour ExpressRoute doit être /28 ou supérieure (/ 27, / 26 etc..). Cela permet de suffisamment d’adresses IP dans ce sous-réseau permet la configuration pour l’utiliser. Dans le portail classique, si vous avez sélectionné la case à cocher Utiliser ExpressRoute, le portail spécifie un sous-réseau passerelle avec /28.  Vous ne pouvez pas modifier le nombre d’adresses CIDR dans le portail classique. Le sous-réseau passerelle s’affichent en tant que **passerelle** dans le portail classique, bien que le nom réel du sous-réseau passerelle créé est en réalité **GatewaySubnet**. Vous pouvez afficher ce nom à l’aide de PowerShell ou dans le portail Azure.

7. Cliquez sur la coche en bas de la page et commence à créer votre réseau virtuel. Lorsqu’elle est terminée, vous verrez **que créé** répertorié sous **statut** sur la page **réseaux** dans le portail classique.

## <a name="gw"></a>Création de la passerelle

1. Dans la page **réseaux** , cliquez sur le réseau virtuel que vous venez de créer, puis cliquez sur **tableau de bord** en haut de la page.

2. Au bas de la page de **tableau de bord** , cliquez sur **Créer une passerelle** , puis sélectionnez **Routage dynamique**. Cliquez sur **Oui** pour confirmer que vous voulez créer une passerelle.

3. Lorsque la passerelle commence la création, vous verrez un message vous indiquant que la passerelle a été démarrée. Il peut prendre jusqu'à 45 minutes pour la passerelle à créer.

4. Créer un lien à un circuit votre réseau. Suivez les instructions de l’article [comment créer un lien VNets aux circuits ExpressRoute](expressroute-howto-linkvnet-classic.md).

## <a name="config"></a>Configurer un VNet classique existant pour ExpressRoute

Si vous disposez déjà d’un VNet classique, vous pouvez le configurer pour vous connecter à ExpressRoute dans le portail classique. Les paramètres sont les mêmes que les sections précédentes, lisez donc ces sections pour vous familiariser avec les valeurs requises. Si vous voulez créer une connexion pouvant coexistence ExpressRoute/Site-à-Site, voir [cet article](expressroute-howto-coexist-classic.md) pour connaître les étapes. Ils sont différents des étapes décrites dans cet article.
 
1. Vous devez créer le réseau local avant que vous mettez à jour le reste de vos paramètres VNet. Pour créer un nouveau réseau local, qui est requis lors de la configuration ExpressRoute via le portail classique, cliquez sur **Nouveau** **>** **Services de réseau** **>** **Réseau virtuel** **>** **réseau local ajouter**. Suivez les étapes de l’Assistant pour créer le réseau local.

2. Utilisez page **configurer** pour mettre à jour le reste des paramètres pour votre VNet et associer la VNet au réseau local.

3. Après avoir configuré les paramètres, accédez à la section [créer la passerelle](#gw) de cet article pour créer la passerelle.


## <a name="next-steps"></a>Étapes suivantes

- Si vous voulez ajouter des machines virtuelles à votre réseau virtuel, voir [Machines virtuelles rubriques d’apprentissage professionnelles](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
- Si vous voulez en savoir plus sur ExpressRoute, consultez la [Vue d’ensemble ExpressRoute](expressroute-introduction.md).


 
