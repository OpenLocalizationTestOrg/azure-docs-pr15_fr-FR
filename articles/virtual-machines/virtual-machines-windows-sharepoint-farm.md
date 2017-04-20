<properties
    pageTitle="Créer des batteries de serveurs SharePoint | Microsoft Azure"
    description="Créer rapidement une nouvelle batterie de serveurs SharePoint 2013 ou 2016 SharePoint dans Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="josephd"/>

# <a name="create-sharepoint-server-farms"></a>Créer des batteries de serveurs SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle classique.

## <a name="sharepoint-2013-farms"></a>Batteries de serveurs SharePoint 2013

Avec le marché du portail Microsoft Azure, vous pouvez créer rapidement préconfigurés batteries de serveurs SharePoint Server 2013. Il peut enregistrer vous beaucoup de temps lorsque vous avez besoin d’une batterie de serveurs SharePoint base ou disponibilité pour un environnement de développement/test ou si vous évaluez SharePoint Server 2013 comme une solution de collaboration pour votre organisation.

> [AZURE.NOTE] L’élément de **Batterie de serveurs SharePoint** sur le marché Azure du portail Azure a été supprimée. Il a été remplacé par les éléments de **SharePoint 2013 non-HA batterie de serveurs** et de **Batterie de serveurs SharePoint 2013 HA** .

La batterie de serveurs SharePoint base est composée de trois machines virtuelles dans cette configuration.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

Vous pouvez utiliser cette configuration de batterie de serveurs pour simplifier la configuration pour le développement de l’application SharePoint ou votre évaluation première de SharePoint 2013.

Pour créer la batterie de serveurs SharePoint (serveur de trois) base :

1. Cliquez sur [ici](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Cliquez sur **déployer**.
3. Dans le volet de **SharePoint 2013 non-HA batterie de serveurs** , cliquez sur **créer**.
4. Spécifier les paramètres sur les étapes du volet **Créer SharePoint 2013 non-HA batterie de serveurs** , puis cliquez sur **créer**.

La batterie de serveurs SharePoint et la disponibilité se compose de neuf machines virtuelles dans cette configuration.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

Vous pouvez utiliser cette configuration de batterie de serveurs pour tester les charges client plus élevées, haute disponibilité du site SharePoint externes et groupes de disponibilité AlwaysOn SQL Server une batterie de serveurs SharePoint. Vous pouvez également utiliser cette configuration pour le développement de l’application SharePoint dans un environnement de disponibilité.

Pour créer une batterie SharePoint disponibilité (neuf-server) :

1. Cliquez sur [ici](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Cliquez sur **déployer**.
3. Dans le volet de la **Batterie de serveurs SharePoint 2013 HA** , cliquez sur **créer**.
4. Spécifier les paramètres sur les sept étapes du volet **Créer 2013 HA batterie de serveurs SharePoint** , puis cliquez sur **créer**.

> [AZURE.NOTE] Vous ne pouvez pas créer la **SharePoint 2013 non-HA batterie de serveurs** ou une **Batterie de serveurs SharePoint 2013 HA** avec une version d’évaluation gratuite Azure.

Le portail Azure crée les deux de ces batteries de serveurs dans un réseau virtuel exclusivement le nuage, avec une présence web via Internet. Il n’existe aucune connexion de réseau privé virtuel ou ExpressRoute site au réseau de votre organisation.

> [AZURE.NOTE] Lorsque vous créez la basic ou batteries de serveurs de SharePoint de disponibilité à l’aide du portail Azure, vous ne pouvez pas spécifier un groupe de ressources existant. Pour contourner cette limitation, créez ces batteries avec Azure PowerShell. Pour plus d’informations, voir [créer SharePoint 2013 développement/test batteries avec Azure PowerShell](https://technet.microsoft.com/library/mt743093.aspx#powershell).

## <a name="sharepoint-2016-farms"></a>Batteries de serveurs SharePoint 2016

Consultez [cet article](https://technet.microsoft.com/library/mt723354.aspx) pour obtenir des instructions générer la batterie de serveurs SharePoint Server 2016 suivant du serveur unique.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>Gérer les batteries de serveurs SharePoint

Vous pouvez administrer les serveurs de ces batteries de serveurs via des connexions de bureau à distance. Pour plus d’informations, voir [Ouvrir une session sur l’ordinateur virtuel](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine).

À partir du site SharePoint Administration centrale, vous pouvez configurer mes sites, les applications SharePoint et autres fonctionnalités. Pour plus d’informations, voir [Configurer SharePoint](http://technet.microsoft.com/library/ee836142.aspx).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [configurations SharePoint](https://technet.microsoft.com/library/dn635309.aspx) supplémentaires dans les services d’infrastructure Azure.
