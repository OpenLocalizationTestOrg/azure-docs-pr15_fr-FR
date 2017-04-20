<properties
   pageTitle="Types de nœuds tissu de service et jeux d’échelle machine virtuelle | Microsoft Azure"
   description="Explique comment les types de nœuds tissu de Service sont liés aux jeux d’échelle machine virtuelle et comment à distance se connecter à une instance machine virtuelle échelle définie ou un nœud de cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>La relation entre les types de nœud tissu de Service et jeux d’échelle Machine virtuelle

Machine virtuelle échelle jeux sont une ressource Azure calculer, que vous pouvez utiliser pour déployer et gérer une collection de machines virtuelles comme un ensemble. Chaque type de nœud défini dans un cluster de tissu de Service a été configurée en tant que jeu d’une échelle machine virtuelle distincte. Chaque type de nœud peut ensuite être mise à l’échelle vers le haut ou vers le bas séparément, présentent différents jeux de ports ouverts et peut contenir des indicateurs de capacité différente.

La capture d’écran suivante montre un cluster qui comporte deux types de nœuds : FrontEnd et serveur principal.  Chaque type de nœud comporte cinq nœuds.

![Capture d’écran d’un cluster qui comporte deux Types de nœuds][NodeTypes]

## <a name="mapping-vm-scale-set-instances-to-nodes"></a>Mappage d’instances machine virtuelle échelle définie à nœuds

Comme vous pouvez le voir ci-dessus, les instances machine virtuelle échelle définie démarrer à partir de l’instance 0, puis se déplace vers le haut. La numérotation est reflétée dans les noms. Par exemple, BackEnd_0 est 0 instance de l’ensemble de serveur principal machine virtuelle échelle. Cet ensemble d’échelle machine virtuelle particulier dispose de cinq instances, nommés BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 et BackEnd_4.

Lorsque vous évoluer une échelle de machine virtuelle définir une nouvelle instance est créée. Le nouveau nom instance machine virtuelle échelle définie sera généralement le nom de la machine virtuelle échelle définie + le numéro d’instance suivant. Dans notre exemple, il est BackEnd_5.


## <a name="mapping-vm-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Mappage d’échelle machine virtuelle définie les programmes d’équilibrage de charge à chaque type de nœud/machine virtuelle échelle ensemble

Si vous avez déployé votre cluster à partir du portail ou que vous avez utilisé l’exemple de modèle de gestionnaire de ressources qui nous fournis, puis lorsque vous recevez une liste de toutes les ressources dans un groupe de ressources que vous verrez les programmes d’équilibrage de charge pour chaque type d’échelle de la machine virtuelle définie ou nœud.

Le nom serait s’intitule : **kg -&lt;nom NodeType&gt;**. Par exemple, g-sfcluster4doc-0, comme indiqué dans cette capture d’écran :


![Ressources][Resources]


## <a name="remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node"></a>Connexion à distance à une instance machine virtuelle échelle définie ou un nœud de cluster
Chaque type de nœud défini dans un cluster a été configurée en tant que jeu d’une échelle machine virtuelle distincte.  Cela signifie que les types de nœuds peuvent être mise à l’échelle vers le haut ou vers le bas séparément et peuvent être effectuées de différentes machine virtuelle références SKU. Contrairement à instance unique machines virtuelles, les instances machine virtuelle échelle définie n’obtient pas une adresse IP virtuelle de leur propre. Donc il peut être un peu difficile lorsque vous recherchez une adresse IP et port que vous pouvez utiliser à distance se connecter à une instance spécifique.

Voici les étapes à suivre pour découvrir les.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Étape 1 : Déterminer l’adresse IP virtuelle pour le type de nœud, puis sur règles de trafic entrant NAT pour RDP

Afin d’obtenir qui, vous devez obtenir les valeurs de règles NAT entrants qui ont été définis dans le cadre de la définition de la ressource pour **Microsoft.Network/loadBalancers**.

Dans le portail, accédez à la carte d’équilibrage de charge, puis sur **paramètres**.

![LBBlade][LBBlade]


Dans **paramètres**, cliquez sur **règles de trafic entrant NAT**. Désormais vous donne l’adresse IP et port que vous pouvez utiliser à distance se connecter à la première instance machine virtuelle échelle définie. Dans la capture d’écran ci-dessous, il est **104.42.106.156** et **3389**

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-vm-scale-set-instancenode"></a>Étape 2 : Rechercher le port que vous pouvez utiliser à distance se connecter à l’instance/nœud de spécifique machine virtuelle échelle définir la sortie

Précédemment dans ce document, j’ai parlé comment les instances machine virtuelle échelle définie établir une correspondance entre les nœuds. Nous allons utiliser que pour identifier le port exact.

Les ports sont affectés dans l’ordre croissant de l’instance machine virtuelle échelle définie. dans mon exemple pour le type de nœud FrontEnd, donc les ports pour chacune des instances de cinq sont les suivantes : Vous devez maintenant effectuer le mappage même pour votre instance machine virtuelle échelle définie.

|**Machine virtuelle échelle définie Instance**|**Port**|
|-----------------------|--------------------------|
|FrontEnd_0|3389|
|FrontEnd_1|3390|
|FrontEnd_2|3391|
|FrontEnd_3|3392|
|FrontEnd_4|3393|
|FrontEnd_5|3394|


### <a name="step-3-remote-connect-to-the-specific-vm-scale-set-instance"></a>Étape 3 : Connecter à distance à l’instance spécifique machine virtuelle échelle définie

Dans la capture d’écran ci-dessous utiliser Connexion Bureau à distance pour vous connecter à la FrontEnd_1 :

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Comment modifier les valeurs de plage de port RDP

### <a name="before-cluster-deployment"></a>Avant le déploiement de cluster

Lorsque vous configurez le cluster à l’aide d’un modèle de gestionnaire de ressources, vous pouvez spécifier la plage dans **inboundNatPools**.

Accédez à la définition de la ressource pour **Microsoft.Network/loadBalancers**. Sous que vous recherchez la description de **inboundNatPools**.  Remplacer les valeurs *frontendPortRangeStart* et *frontendPortRangeEnd* .

![InboundNatPools][InboundNatPools]


### <a name="after-cluster-deployment"></a>Après le déploiement de cluster
C’est un peu plus longue et peut entraîner les machines virtuelles prise recyclés. Vous devrez maintenant définir de nouvelles valeurs à l’aide de PowerShell Azure. Assurez-vous que Azure PowerShell 1.0 ou version ultérieure est installé sur votre ordinateur. Si vous n’avez pas fait auparavant, j’ai recommandons vivement que vous suivez les étapes décrites dans [comment installer et configurer Azure PowerShell.](../powershell-install-configure.md)

Connectez-vous à votre compte Azure. Si cette commande PowerShell échoue pour une raison quelconque, vous devez vérifier si vous disposez de PowerShell Azure installé correctement.

```
Login-AzureRmAccount
```

Exécutez la procédure suivante pour obtenir des détails sur votre équilibrage de charge et que les valeurs de la description de **inboundNatPools**:

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

Définissez maintenant *frontendPortRangeEnd* et *frontendPortRangeStart* sur les valeurs souhaitées.

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la fonctionnalité « Déployer où que vous soyez » et une comparaison avec clusters gérées Azure](service-fabric-deploy-anywhere.md)
- [Sécurité cluster](service-fabric-cluster-security.md)
- [Service TISSU SDK et mise en route](service-fabric-get-started.md)


<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
