<properties 
   pageTitle="Configurez une passerelle VPN dans le portail classique Azure | Microsoft Azure"
   description="Cet article décrit vous aide à la configuration de votre réseau virtuel passerelle VPN et la modification d’une type de routage VPN passerelle."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/11/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vpn-gateway-for-the-classic-deployment-model"></a>Configurez une passerelle VPN pour le modèle de déploiement classique


Si vous voulez créer une connexion sécurisée entre locaux entre Azure et votre emplacement en local, vous devez configurer une connexion de passerelle VPN. Dans le modèle de déploiement classique, une passerelle peut être un des deux types de routage VPN : statique ou dynamique. Le type que vous choisissez dépend de votre plan de conception de réseau et le périphérique VPN local que vous voulez utiliser. 

Par exemple, certaines options de connectivité, par exemple une connexion point-à-site, nécessitent une passerelle routage dynamique. Si vous voulez configurer votre passerelle pour prendre en charge les connexions point-à-site (P2S) et une connexion (S2S) à un site, vous devez configurer une passerelle routage dynamique alors que le site à peut être configuré avec un type de routage VPN passerelle. 

En outre, devez vous assurer que le périphérique que vous voulez utiliser pour votre connexion prend en charge le type de routage VPN que vous souhaitez créer. Voir [à propos des appareils VPN](vpn-gateway-about-vpn-devices.md).


**À propos de cet article** 

Cet article est destiné le modèle de déploiement classique à l’aide du [portail classique](https://manage.windowsazure.com) (pas le portail Azure). 

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-overview"></a>Présentation de la configuration

Les étapes suivantes vous guident dans le processus de configuration de votre passerelle VPN dans le portail classique Azure. Ces étapes s’appliquent aux passerelles pour les réseaux virtuels qui ont été créés à l’aide du modèle de déploiement classique. Pour l’instant, tous les paramètres de configuration pour les passerelles sont disponibles dans le portail Azure. Lorsqu’elles sont, nous allons créer un nouveau jeu d’instructions qui s’appliquent au portail Azure.


1. [Créer une passerelle VPN pour votre VNet](#create-a-vpn-gateway)

1. [Collecter les informations de configuration de vos périphériques VPN](#gather-information-for-your-vpn-device-configuration)

1. [Configurer votre périphérique VPN](#configure-your-vpn-device)

1. [Vérifiez vos plages réseau local et l’adresse IP de passerelle VPN](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

### <a name="before-you-begin"></a>Avant de commencer

Avant de configurer votre passerelle, vous devez tout d’abord créer votre réseau virtuel. Pour obtenir la procédure créer un réseau virtuel pour la connectivité entre local, voir [configurer un réseau avec une connexion VPN de site à virtuel](vpn-gateway-site-to-site-create.md)ou [configurer un réseau avec une connexion VPN point-à-site virtuel](vpn-gateway-point-to-site-create.md). Ensuite, procédez comme suit pour configurer la passerelle VPN et collecter les informations nécessaires configurer votre périphérique VPN. 

Si vous disposez déjà d’une passerelle VPN et que vous voulez modifier le type de routage VPN, Découvrez [comment modifier le type de routage VPN de la passerelle](#how-to-change-the-vpn-routing-type-for-your-gateway).

## <a name="create-a-vpn-gateway"></a>Créer une passerelle VPN

1. Dans le [portail classique Azure](https://manage.windowsazure.com), dans la page **réseaux** , vérifiez que la colonne statut pour votre réseau virtuel est **créé**.

1. Dans la colonne **nom** , cliquez sur le nom de votre réseau virtuel.

1. Dans la page **tableau de bord** , notez que cette VNet n’a pas une passerelle encore configurée. Vous verrez ce statut que vous suivez les étapes pour configurer votre passerelle.

![Passerelle ne pas créé](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)


Ensuite, dans la partie inférieure de la page, cliquez sur **Créer une passerelle**. Vous pouvez sélectionner *Routage statique* ou *Dynamique routage*. Le type de routage VPN que vous sélectionnez dépend de plusieurs facteurs. Par exemple, que votre périphérique VPN prend en charge et si vous avez besoin prendre en charge les connexions point-à-site. Vérifiez que [Sur les périphériques VPN pour la connectivité réseau virtuel](vpn-gateway-about-vpn-devices.md) pour vérifier le type de routage VPN dont vous avez besoin. Une fois que la passerelle a été créée, vous ne pouvez pas modifier entre les types de routage VPN passerelle sans supprimer et recréer la passerelle. Lorsque le système vous invite à confirmer que vous souhaitez la passerelle créée, cliquez sur **Oui**.

![Type de passerelle VPN Routage](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Lors de la création de votre passerelle, vous pouvez remarquer le graphique de la passerelle dans la page change au jaune indique la *Création d’une passerelle*. Il peut prendre jusqu'à 45 minutes pour la passerelle à créer. Attendez que la passerelle se termine avant de le déplacer vers l’avant avec d’autres paramètres de configuration.

![Création de la passerelle](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Lorsque la passerelle prend la *connexion*, vous pouvez collecter les informations que vous avez besoin pour votre appareil VPN.

![Connexion de la passerelle](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="gather-information-for-your-vpn-device-configuration"></a>Collecter les informations de configuration de vos périphériques VPN

Une fois que la passerelle a été créée, collecter les informations de configuration de vos périphériques VPN. Ces informations sont trouve dans la page **tableau de bord** de votre réseau virtuel :

1. **Adresse IP de la passerelle-** L’adresse IP, vous pouvez trouver dans la page **tableau de bord** . Vous ne pourrez voir qu’une fois que votre passerelle a terminé de créer.

1. **Clé partagée-** Cliquez sur **Gérer la clé** dans la partie inférieure de l’écran. Cliquez sur l’icône en regard de la clé à copier dans le Presse-papiers, coller, puis enregistrez la clé. Ce bouton fonctionne uniquement lorsqu’il y a un seul tunnel VPN S2S. Si vous avez plusieurs tunnel S2S VPN, utilisez l' *Obtenir virtuel réseau partagé clé de passerelle* API ou l’applet de commande PowerShell.

![Gérer la clé](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)


## <a name="configure-your-vpn-device"></a>Configurer votre périphérique VPN

Une fois les étapes précédentes, vous ou votre administrateur réseau devrez configurer l’appareil VPN afin de créer la connexion. Pour plus d’informations sur les périphériques VPN, consultez [Sur les périphériques VPN pour la connectivité réseau virtuel](vpn-gateway-about-vpn-devices.md) .

Une fois que le périphérique VPN a été configuré, vous pouvez afficher vos informations de connexion mis à jour dans la page tableau de bord pour votre VNet.

Vous pouvez également exécuter une des commandes suivantes pour tester votre connexion :

|                      | Cisco ASA             | Cisco ISR/récupération automatique du système         | Juniper SSG/ISG | Juniper SRX/J                            |
|----------------------|-----------------------|-----------------------|-----------------|------------------------------------------|
| **Vérifier sa en mode principal**  | afficher et isakmp sa | afficher et isakmp sa | obtenir des cookies ike  | afficher la sécurité association de sécurité ike   |
| **Vérifier sa de mode rapide** | afficher le chiffrement ipsec sa  | afficher le chiffrement ipsec sa  | obtenir sa          | afficher la sécurité ipsec-association de sécurité |


## <a name="verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>Vérifiez vos plages réseau local et l’adresse IP de passerelle VPN

### <a name="verify-your-vpn-gateway-ip-address"></a>Vérifier votre adresse IP de passerelle VPN

Pour la passerelle pour se connecter, l’adresse IP de votre appareil VPN doit être configuré correctement pour le réseau Local que vous avez spécifié pour votre configuration croisée locaux. En règle générale, cela est configuré au cours du processus de configuration de site à site. Toutefois, si vous avez utilisé ce réseau local avec un autre périphérique, ou l’adresse IP a été modifiée pour ce réseau local, modifiez les paramètres pour spécifier l’adresse IP de la passerelle correcte.

1. Pour vérifier votre adresse IP de passerelle, cliquez sur **les réseaux** dans le volet gauche du portail, puis sélectionnez **Réseaux locaux** en haut de la page. Vous verrez l’adresse de la passerelle VPN pour chaque réseau local que vous avez créé. Pour modifier l’adresse IP, sélectionnez le VNet, puis cliquez sur **Modifier** dans la partie inférieure de la page.

1. Dans la page **spécifier les détails de votre réseau local** , modifiez l’adresse IP, puis cliquez sur la flèche en bas de la page suivante.

1. Dans la page **spécifier l’espace d’adressage** , cliquez sur la coche dans le coin inférieur droit pour enregistrer vos paramètres.

### <a name="verify-the-address-ranges-for-your-local-networks"></a>Vérifier les plages d’adresses pour vos réseaux local

Pour le trafic de flux via la passerelle vers votre emplacement en local, vous devez vérifier que chaque plage d’adresses IP est spécifié. Chaque plage doit être répertorié dans votre configuration Azure **Réseaux locaux** . Selon la configuration du réseau de votre emplacement en local, cela peut s’avérer quelque peu volumineux. Le trafic qui est lié à une adresse IP qui est contenue dans les plages répertoriées est envoyé via la passerelle VPN réseau virtuel. Les plages de liste que vous n’avez pas à être des plages privés, bien que vous voulez vérifier que votre configuration locale peut recevoir du trafic entrant.

Pour ajouter ou modifier les plages pour un réseau Local, procédez comme suit.

1. Pour modifier les plages d’adresses IP pour un réseau local, cliquez sur **les réseaux** dans le volet gauche du portail, puis sélectionnez **Réseaux locaux** en haut de la page. Dans le portail, le moyen le plus simple pour afficher les plages que vous avez répertoriés se trouve sur la page **Modifier** . Pour afficher vos plages, sélectionnez le VNet et cliquez sur **Modifier** dans la partie inférieure de la page.

1. Dans la page **spécifier les détails de votre réseau local** , n’apportez les modifications. Cliquez sur la flèche en bas de la page suivante.

1. Dans la page **spécifier l’espace d’adressage** , apportez vos modifications d’espace d’adresse réseau. Cliquez ensuite sur la coche pour enregistrer votre configuration.

## <a name="how-to-view-gateway-traffic"></a>Comment afficher le trafic de passerelle

Vous pouvez afficher votre passerelle et le trafic de passerelle à partir de votre réseau virtuel **tableau de bord** .

Dans la page **tableau de bord** , vous pouvez afficher les éléments suivants :

- La quantité de données qui sont écoule à travers votre passerelle, données et des données.

- Les noms des serveurs DNS qui sont spécifiés pour votre réseau virtuel.

- La connexion entre votre passerelle et à votre appareil VPN.

- La clé partagée est utilisée pour configurer votre connexion passerelle à votre appareil VPN.


## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>Comment modifier le type de routage VPN pour votre passerelle

Étant donné que certaines configurations de connectivité sont disponibles uniquement pour certains types de routage de passerelle, il est possible que vous deviez modifier la passerelle type routage VPN d’une passerelle VPN existante. Par exemple, vous souhaiterez peut-être ajouter une connectivité point-à-site à une connexion à un site existante qui dispose d’une passerelle statique. Connexions point-à-site nécessitent une passerelle dynamique. Cela signifie configurer une connexion P2S, vous devez modifier votre type de routage VPN passerelle depuis statique dynamique.

Si vous devez modifier une type de routage VPN passerelle, vous devez supprimer la passerelle existante et puis créer une nouvelle passerelle avec le nouveau type de routage. Vous n’avez pas besoin de supprimer l’ensemble du réseau virtuel pour modifier le type de routage de passerelle.

Avant de modifier votre type de routage VPN passerelle, veillez à vérifier que votre appareil VPN prend en charge le type de routage que vous souhaitez utiliser. Pour télécharger les nouveaux exemples configuration routage et vérifier la configuration du dispositif VPN, voir [Sur les périphériques VPN pour la connectivité réseau virtuel](vpn-gateway-about-vpn-devices.md).

>[AZURE.IMPORTANT] Lorsque vous supprimez une passerelle VPN réseau virtuel, l’adresse IP virtuelle affectée à la passerelle est publié. Lorsque vous recréez la passerelle, un nouveau VIP est affecté à celui-ci.

1. **Supprimer la passerelle VPN existante.**

    Dans la page **tableau de bord** pour votre réseau virtuel, naviguez jusqu’au bas de la page, puis cliquez sur **Supprimer la passerelle**. Attendez que la notification que la passerelle a été supprimée. Une fois que vous recevez la notification sur l’écran que votre passerelle a été supprimée, vous pouvez créer une nouvelle passerelle.

1. **Création d’une passerelle VPN.**

    Utilisez la procédure dans la partie supérieure de la page pour créer une nouvelle passerelle : [créer une passerelle VPN](#create-a-vpn-gateway).


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ajouter des machines virtuelles à votre réseau virtuel. Découvrez [comment créer une machine virtuelle personnalisée](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Si vous souhaitez configurer une connexion VPN point-à-site, voir [configurer une connexion VPN point-à-site](vpn-gateway-point-to-site-create.md).

 
