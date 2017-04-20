<properties 
   pageTitle="Résoudre les problèmes de groupes de sécurité réseau - portail | Microsoft Azure"
   description="Découvrez comment résoudre les problèmes réseau les groupes de sécurité dans le modèle de déploiement d’Azure le Gestionnaire de ressources à l’aide du portail Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Résoudre les problèmes de groupes de sécurité réseau à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

Si vous configuré les groupes de sécurité réseau (NSGs) sur votre machine virtuelle () et que vous rencontrez des problèmes de connectivité machine virtuelle, cet article fournit une vue d’ensemble des fonctionnalités de diagnostic pour NSGs afin de résoudre les autres.

NSGs vous permettent de contrôler les types de trafic activer ce flux entrant et sortant vos machines virtuelles (machines virtuelles). NSGs peuvent être appliqués aux sous-réseaux dans un réseau virtuel Azure (VNet), les interfaces réseau (carte réseau) ou les deux. Les règles efficaces appliqués à une carte réseau sont une agrégation des règles qui existent dans les NSGs appliqués à une carte réseau et le sous-réseau à qu'il est connecté. Les règles sur ces NSGs peuvent parfois entrent en conflit et avoir un impact sur la connectivité réseau d’un ordinateur virtuel.  

Vous pouvez afficher toutes les règles de sécurité efficaces à partir de votre NSGs, comme appliqué sur cartes réseau de votre ordinateur virtuel. Cet article vous explique comment résoudre les problèmes de connectivité machine virtuelle à l’aide de ces règles dans le modèle de déploiement d’Azure le Gestionnaire de ressources. Si vous n’êtes pas familiarisé avec les concepts VNet et NSG, lisez les articles de vue d’ensemble du [réseau virtuelles](virtual-networks-overview.md) et des [groupes de sécurité réseau](virtual-networks-nsg.md) .

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>À l’aide de règles de sécurité efficaces pour résoudre les problèmes de flux de trafic machine virtuelle

Le scénario qui suit est un exemple d’un problème de connexion courants :

Un ordinateur virtuel nommé *VM1* fait partie d’un sous-réseau nommé *Subnet1* au sein d’un VNet nommé *WestUS VNet1*. Une tentative de se connecter à la machine virtuelle à l’aide de RDP sur le port TCP 3389 échoue. NSGs sont appliquées à la carte réseau *VM1 NIC1* et le sous-réseau *Subnet1*. Le trafic vers le port TCP 3389 est autorisé dans le NSG associé à l’interface réseau *VM1 NIC1*, mais TCP ping vers échoue de Port3389 de VM1.

Bien que cet exemple utilise le port TCP 3389, les étapes suivantes peuvent servir à déterminer les échecs de connexion entrant et sortant sur n’importe quel port.

### <a name="view-effective-security-rules-for-a-virtual-machine"></a>Afficher les règles de sécurité efficace pour une machine virtuelle

Effectuez les étapes suivantes pour résoudre les problèmes de NSGs pour une machine virtuelle :

Vous pouvez afficher la liste complète des règles de sécurité efficace sur une carte réseau, à partir de la machine virtuelle elle-même. Vous pouvez également ajouter, modifier et supprimer des règles NSG carte réseau et sous-réseau de la carte de règles efficaces, si vous disposez des autorisations pour effectuer ces opérations.

1. Connectez-vous au portail Azure à https://portal.azure.com.
2. Cliquez sur **plusieurs services**, puis cliquez sur **machines virtuelles** dans la liste qui s’affiche.
3. Sélectionnez une machine virtuelle pour résoudre les problèmes dans la liste qui s’affiche et une carte machine virtuelle avec les options s’affiche.
4. Cliquez sur **diagnostiquer & résoudre les problèmes** , puis sélectionnez un problème courant. Dans cet exemple, **je ne parviens pas à se connecter à mon machine virtuelle Windows** est sélectionnée. 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)

5. Étapes apparaissent sous le problème, comme illustré dans l’image suivante : 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)

    Cliquez sur *règles de groupe de sécurité efficace* dans la liste des étapes à suivre.

6. La carte **obtention des règles de sécurité** s’affiche, comme le montre l’illustration suivante :

    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)

    Remarquez les sections suivantes de l’image :

    - **Étendue :** La valeur *VM1*, la machine virtuelle sélectionnée à l’étape 3.
    - **Interface réseau :** *VM1 NIC1* est sélectionnée. Une machine virtuelle peut avoir plusieurs interfaces réseau (carte réseau). Chaque carte réseau peut avoir des règles de sécurité efficace unique. Lors de la résolution des problèmes, vous devrez peut-être afficher les règles de sécurité efficace pour chaque carte réseau.
    - **Associés NSGs :** NSGs peuvent être appliqués à la carte réseau et le sous-réseau à que la carte réseau est connectée. Dans l’image, un NSG a été appliqué à la carte réseau et le sous-réseau à qu'il est connecté. Vous pouvez cliquer sur les noms NSG à modifier directement les règles dans le NSGs.
    - **Onglet VM1 nsg :** La liste des règles affichées dans l’image est pour la NSG appliqué à la carte réseau. Plusieurs règles par défaut sont créés par Azure chaque fois qu’un NSG est créée. Vous ne pouvez pas supprimer les règles par défaut, mais vous pouvez les remplacer par les règles de priorité. Pour en savoir plus sur les règles par défaut, consultez l’article [présentation NSG](virtual-networks-nsg.md#default-rules) .
    - **Colonne DESTINATION :** Certaines règles ont du texte dans la colonne, tandis que d’autres utilisateurs ont les préfixes d’adresse. Le texte est le nom de balises par défaut appliqué à la règle de sécurité lorsqu’il a été créé. Les balises sont fournies par le système identificateurs qui représentent plusieurs préfixes. Sélection d’une règle avec une balise, par exemple *AllowInternetOutBound*, répertorie les préfixes dans la carte **préfixes d’adresse** .
    - **Télécharger :** La liste des règles peut être longue. Vous pouvez télécharger un fichier .csv des règles pour une analyse en mode hors connexion en cliquant sur **Télécharger** le fichier.
    - **AllowRDP** Règle de trafic entrant : cette règle autorise les connexions RDP de la machine virtuelle.
7. Cliquez sur l’onglet **Subnet1 NSG** pour afficher les règles efficaces à partir de la NSG appliqué au sous-réseau, comme le montre l’illustration suivante : 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)

    Notez la règle **entrant** *denyRDP* . Règles de trafic entrant appliqués au sous-réseau sont évaluées avant les règles appliquées à l’interface réseau. Étant donné que la règle de refus est appliquée sur le sous-réseau, la demande pour vous connecter à TCP 3389 échoue, car la règle d’autorisation à la carte réseau n’est jamais évaluée. 

    La règle *denyRDP* est la raison pour laquelle pourquoi ignore la connexion RDP. Suppression devrait résoudre le problème.

    >[AZURE.NOTE]Si la machine virtuelle associée à la carte réseau n’est pas en cours d’exécution, ou NSGs n’ont pas été appliqués à la carte réseau ou sous-réseau, aucune règle n’est affichées.

8. Pour modifier les règles NSG, cliquez sur *Subnet1 NSG* dans la section **NSGs associé** .
   Cette action ouvre la carte **Subnet1 NSG** . Vous pouvez modifier directement les règles en cliquant sur **les règles de sécurité entrante**.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)

9. Après avoir supprimé la règle de trafic entrant *denyRDP* dans la **Subnet1 NSG** et ajout d’une règle *allowRDP* , la liste des règles efficaces ressemble à l’illustration suivante :

    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)

    Vérifiez que le port TCP 3389 est ouvert en ouvrant une connexion RDP à la machine virtuelle ou à l’aide de l’outil PsPing. Vous pouvez Apprenez-en davantage sur PsPing en lisant la [page de téléchargement PsPing](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="view-effective-security-rules-for-a-network-interface"></a>Afficher les règles de sécurité efficace pour une interface réseau

Si votre flux de trafic machine virtuelle est touchée pour une carte réseau spécifique, vous pouvez afficher une liste complète des règles efficaces pour la carte réseau à partir du contexte d’interfaces réseau en procédant comme suit :

1. Connectez-vous au portail Azure à https://portal.azure.com.
2. Cliquez sur **plusieurs services**, puis cliquez sur **interfaces réseau** dans la liste qui s’affiche.
3. Sélectionnez une interface réseau. Dans l’illustration suivante, une carte réseau nommée *VM1 NIC1* est sélectionnée.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)

    Notez que l' **étendue** est définie à l’interface réseau sélectionnée. Pour en savoir plus sur les mentions spéciales, lisez l’étape 6 de la section **Résoudre les problèmes NSGs pour une machine virtuelle** de cet article.

    >[AZURE.NOTE] Si un NSG est supprimé à partir d’une interface réseau, les NSG constitue toujours efficace sur la carte donnée. Dans ce cas, le résultat serait afficher uniquement les règles à partir du sous-réseau NSG. Règles s’affichent uniquement si la carte réseau est connectée à un ordinateur virtuel.

4. Vous pouvez modifier directement les règles pour NSGs associés à une carte réseau et un sous-réseau. Pour en savoir plus, voir l’étape 8 de la section **Afficher les règles de sécurité efficace pour une machine virtuelle** de cet article.

## <a name="view-effective-security-rules-for-a-network-security-group-nsg"></a>Afficher les règles de sécurité efficace pour un groupe de sécurité réseau (NSG)

Lorsque vous modifiez les règles NSG, vous souhaiterez peut-être vérifier l’impact de règles ajouté sur un ordinateur virtuel particulier. Vous pouvez afficher une liste complète des règles de sécurité efficace pour toutes les cartes réseau un NSG donnée s’applique, sans avoir à basculer le contexte de la carte NSG donnée. Pour résoudre les règles efficaces au sein d’un NSG, procédez comme suit :

1. Connectez-vous au portail Azure à https://portal.azure.com.
2. Cliquez sur **plusieurs services**, puis cliquez sur **groupes de sécurité réseau** dans la liste qui s’affiche.
3. Sélectionnez un NSG. Dans l’image suivante, un NSG nommé VM1 nsg a été activé.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)

    Remarquez les sections suivantes de l’image précédente :

    - **Étendue :** Définissez la NSG sélectionné.
    - **Machine virtuelle :** Lorsqu’un NSG est appliqué à un sous-réseau, elle est appliquée à toutes les interfaces réseau joints à tous les ordinateurs virtuels connectés au sous-réseau. Cette liste affiche tous les ordinateurs virtuels cette NSG s’applique à. Vous pouvez sélectionner n’importe quel ordinateur virtuel dans la liste.

    >[AZURE.NOTE] Si un NSG est appliqué à uniquement un sous-réseau vide, machines virtuelles ne sont pas répertoriés. Si un NSG est appliqué à une carte réseau qui n’est pas associée à une machine virtuelle, ces cartes réseau pas apparaîtront également. 
    - **Interface réseau :** Une machine virtuelle peut avoir plusieurs interfaces réseau. Vous pouvez sélectionner une interface réseau attachée à la machine virtuelle sélectionnée.
    - **AssociatedNSGs :** À tout moment, une carte réseau peut avoir jusqu'à deux NSGs efficaces, appliqué à la carte réseau et l’autre au sous-réseau. Bien que l’étendue sélectionnée est VM1-nsg, si la carte réseau a un sous-réseau efficace NSG, le résultat affiche les deux NSGs.
4. Vous pouvez modifier directement les règles pour NSGs associés à une carte réseau ou sous-réseau. Pour en savoir plus, voir l’étape 8 de la section **Afficher les règles de sécurité efficace pour une machine virtuelle** de cet article.

Pour en savoir plus sur les mentions spéciales, lisez l’étape 6 de la section **Afficher les règles de sécurité efficace pour une machine virtuelle** de cet article.

>[AZURE.NOTE] Bien que sous-réseau et la carte réseau peuvent contenir uniquement une que NSG est appliquée, un NSG peut être associé à plusieurs cartes réseau et plusieurs sous réseaux.

## <a name="considerations"></a>Considérations relatives à la

Lors de la résolution des problèmes de connectivité, tenez compte des points suivants :

- Règles NSG par défaut seront bloquant l’accès entrant à partir d’internet et autoriser uniquement VNet le trafic entrant. Règles doivent être ajoutés explicitement pour autoriser l’accès entrant à partir d’Internet, selon les besoins.
- S’il n’y a aucune règle de sécurité NSG à l’origine de la connectivité réseau d’un ordinateur virtuel échec, le problème peut être dû à :
    - Logiciel pare-feu qui s’exécutent dans le système d’exploitation de la machine virtuelle
    - Itinéraires configurés pour l’authentification multifacteur ou le trafic local. Le trafic Internet peut être redirigé vers local via forcé-tunnel. Une connexion RDP/SSH à partir d’Internet vers votre ordinateur virtuel peut ne pas fonctionne avec ce paramètre, selon la façon dont le matériel réseau local gère ce trafic. Consultez l’article [Itinéraires de résolution des problèmes](virtual-network-routes-troubleshoot-powershell.md) pour apprendre à diagnostiquer les problèmes d’itinéraire qui peuvent être empêchant le flux du trafic entrant et sortant de la machine virtuelle. 
- Si vous avez ressources VNets, par défaut, la balise VIRTUAL_NETWORK se développe automatiquement pour inclure les préfixes de ressources VNets. Vous pouvez afficher ces préfixes dans la liste **ExpandedAddressPrefix** , pour résoudre les problèmes liés à la connectivité homologation VNet. 
- Règles de sécurité efficace sont affichent uniquement s’il existe une NSG associé de la machine virtuelle carte réseau et ou sous-réseau. 
- Si il n’y a aucune NSGs associées à la carte réseau ou sous-réseau et que vous avez une adresse IP publique affectée à votre ordinateur virtuel, tous les ports seront ouverts pour un accès entrant et sortant. Si la machine virtuelle comporte une adresse IP publique, appliquer NSGs à la carte réseau ou sous-réseau est fortement recommandé.