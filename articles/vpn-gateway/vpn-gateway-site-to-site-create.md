<properties
   pageTitle="Créer un réseau virtuel avec une connexion de passerelle VPN de site à l’aide du portail classique Azure | Microsoft Azure"
   description="Créer un VNet avec une connexion S2S VPN passerelle pour effectuer locaux et configurations hybrides à l’aide du modèle de déploiement classique."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-classic-portal"></a>Créer un VNet avec une connexion de Site à l’aide du portail classique Azure

> [AZURE.SELECTOR]
- [Responsable de ressources - portail Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Classique - portail classique](vpn-gateway-site-to-site-create.md)

Cet article vous guide dans la création d’un réseau virtuel et une connexion à un site à VPN passerelle à votre réseau local à l’aide du portail classique et le modèle de déploiement classique. Connexions de Site à peuvent être utilisées pour croisée local et hybride configurations.

![Diagramme de Site à] (./media/vpn-gateway-site-to-site-create/site2site.png "site à")


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modèles de déploiement et méthodes pour les connexions de Site à

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Le tableau suivant indique les modèles de déploiement actuellement disponibles et les méthodes pour les configurations de Site à. Lorsqu’un article avec des étapes de configuration est disponible, nous attacher directement à partir de ce tableau.

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurations supplémentaires 

Si vous voulez vous connecter VNets ensemble, voir [configurer une connexion VNet-VNet pour le modèle de déploiement classique](virtual-networks-configure-vnet-to-vnet-connection.md). Si vous souhaitez ajouter une connexion à un Site à un VNet contenant déjà une connexion, voir [Ajouter une connexion S2S à un VNet avec une connexion de passerelle VPN existante](vpn-gateway-multi-site.md).
 
## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous avez les éléments suivants avant de commencer la configuration.

- Un périphérique VPN compatible et une personne qui est en mesure de configurer. Voir [à propos des appareils VPN](vpn-gateway-about-vpn-devices.md). Si vous ne familiarisé avec la configuration de votre appareil VPN, ou que vous ne connaissez pas l’adresse IP plages situées dans vos locaux configuration du réseau, vous devez coordonner avec une personne qui pourra vous fournir ces informations.

- Adresse IP publique tourné vers l’extérieur de votre périphérique VPN. Cette adresse IP ne peut pas être située derrière un NAT.

- Un abonnement Azure. Si vous n’avez pas un abonnement Azure, vous pouvez activer votre [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrivez-vous vers le haut pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).


## <a name="CreateVNet"></a>Créer votre réseau virtuel

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com/).

2. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services de réseau**, puis cliquez sur **Réseau virtuel**. Cliquez sur **Créer personnalisée** pour lancer l’Assistant de configuration.

3. Pour créer votre VNet, entrez vos paramètres de configuration dans les pages suivantes :

## <a name="Details"></a>Page de détails de réseau virtuel

Entrez les informations suivantes :

- **Nom**: nommez votre réseau virtuel. Par exemple, *EastUSVNet*. Vous allez utiliser ce nom de réseau virtuel lorsque vous déployez votre instances machines virtuelles et PaaS, et vous préférerez peut-être pas pour que le nom plus compliqué.
- **Emplacement**: l’emplacement est directement lié à l’emplacement physique (région) où vous souhaitez vos ressources (machines virtuelles) réside. Par exemple, si vous souhaitez que les ordinateurs virtuels que vous déployez sur ce réseau virtuel doit être physiquement situé dans *Le fuseau horaire*, sélectionnez cet emplacement. Vous ne pouvez pas modifier la zone associée à votre réseau virtuel après sa création.

## <a name="DNS"></a>Serveurs DNS et page de connectivité VPN

Entrez les informations suivantes, puis cliquez sur la flèche suivant dans le coin inférieur droit.

- **Serveurs DNS**: entrez le nom du serveur DNS et l’adresse IP, ou sélectionnez un serveur DNS précédemment enregistré dans le menu contextuel. Ce paramètre ne crée pas un serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de nom pour ce réseau virtuel.
- **Configurer une connexion VPN Site à**: activez la case à cocher pour **configurer un réseau privé virtuel de site à**.
- **Réseau local**: un réseau local représente votre emplacement physique local. Vous pouvez sélectionner un réseau local que vous avez créé précédemment, ou vous pouvez créer un nouveau réseau local. Toutefois, si vous choisissez d’utiliser un réseau local que vous avez créé précédemment, accédez à la page de configuration de **Réseaux locaux** et vérifiez que l’adresse IP appareil VPN (adresses IPv4 en vis-à-vis public) pour le périphérique VPN est exacte.

## <a name="Connectivity"></a>Page de site à connectivité

Si vous créez un nouveau réseau local, vous verrez la page de **Site à connectivité** . Si vous souhaitez utiliser un réseau local que vous avez créé précédemment, cette page n’apparaît pas dans l’Assistant et vous pouvez passer à la section suivante.

Entrez les informations suivantes, puis cliquez sur la flèche suivant.

-   **Nom**: le nom que vous voulez appeler votre local (local) réseau de site.
-   **Adresse IP du périphérique VPN**: l’adresse IPv4 en regard de votre appareil VPN local que vous utilisez pour vous connecter à Azure. Le périphérique VPN ne peut pas être situé derrière un NAT.
-   **Espace d’adressage**: inclure démarrage IP et CIDR (nombre d’adresses). Vous indiquez l’adresse range(s) que vous souhaitez envoyer via la passerelle réseau virtuel pour votre emplacement local en local. Si une adresse IP de destination est comprise dans les plages que vous spécifiez ici, il est routé via la passerelle réseau virtuel.
-   **Ajouter un espace adresse**: Si vous avez plusieurs plages d’adresses que vous souhaitez être envoyé via la passerelle réseau virtuel, spécifiez chaque plage d’adresses supplémentaires. Vous pouvez ajouter ou supprimer des plages ultérieurement dans la page **Réseau Local** .

## <a name="Address"></a>Page d’espaces réseau virtuel adresse

Spécifiez la plage d’adresses que vous souhaitez utiliser pour votre réseau virtuel. Voici les adresses IP dynamiques (DIP) doivent être affectées pour les ordinateurs virtuels et d’autres instances de rôles que vous déployez sur ce réseau virtuel.

Il est particulièrement important sélectionner une plage qui ne se chevauche pas avec n’importe lequel des plages qui sont utilisés pour votre réseau local. Vous devez coordonner avec votre administrateur réseau. Votre administrateur réseau devra peut-être imaginer une plage d’adresses IP à partir de votre espace d’adressage réseau local pour pouvoir utiliser pour votre réseau virtuel.

Entrez les informations suivantes, puis cliquez sur la coche dans le coin inférieur droit pour configurer votre réseau.

- **Espace d’adressage**: inclure démarrage IP et le nombre d’adresses. Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas un des espaces d’adressage que vous avez sur votre réseau local.
- **Ajouter sous-réseau**: IP inclure de début et le nombre d’adresses. Sous-réseaux supplémentaires ne sont pas obligatoires, mais vous voudrez peut-être créer un sous-réseau séparé pour les machines virtuelles qui auront DIP statique. Ou vous souhaiterez peut-être avoir vos ordinateurs virtuels dans un sous-réseau distinct de vos autres instances de rôle.
- **Sous-réseau de passerelle ajouter**: cliquez ici pour ajouter le sous-réseau passerelle. Le sous-réseau passerelle est utilisé uniquement pour la passerelle réseau virtuel et est nécessaire pour cette configuration.

Cliquez sur la coche en bas de la page et commence à créer votre réseau virtuel. Lorsqu’elle est terminée, vous verrez **que créé** répertorié sous **statut** sur la page **réseaux** dans le portail classique Azure. Une fois la VNet a été créé, vous pouvez ensuite configurer votre passerelle réseau virtuel.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="VNetGateway"></a>Configurez votre passerelle réseau virtuel

Configuration de la passerelle réseau virtuel pour créer une connexion à un site sécurisée. Voir [configurer une passerelle réseau virtuel dans le portail classique Azure](vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="next-steps"></a>Étapes suivantes

Une fois que votre connexion est terminée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuel. Consultez la documentation de [Machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/) pour plus d’informations.
