<properties 
   pageTitle="Configurer le système DNS entre deux réseaux virtuels Azure | Microsoft Azure" 
   description="Découvrez comment configurer les connexions VPN et résolution de noms de domaine entre deux réseaux virtuels et comment configurer HBase geo-réplication." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/28/2016"
   ms.author="jgao"/>

# <a name="configure-dns-between-two-azure-virtual-networks"></a>Configurer le système DNS entre deux réseaux virtuels Azure

> [AZURE.SELECTOR]
- [Configurer la connectivité VPN](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurer le système DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurer la réplication HBase](hdinsight-hbase-geo-replication.md) 


Découvrez comment ajouter et configurer des serveurs DNS à Azure réseaux virtuels pour gérer la résolution de nom au sein et entre les réseaux virtuels.

Ce didacticiel est la deuxième partie de la [série] [ hdinsight-hbase-geo-replication] sur la création de réplication geo HBase :

- [Configurer une connectivité VPN entre deux réseaux virtuels][hdinsight-hbase-geo-replication-vnet]
- Configurer le système DNS pour les réseaux virtuels (ce didacticiel)
- [Configurer la réplication de geo HBase][hdinsight-hbase-geo-replication]


Le diagramme suivant illustre les deux réseaux virtuels vous avez créé à [configurer une connectivité VPN entre deux réseaux virtuels][hdinsight-hbase-geo-replication-vnet]:

![Diagramme de réseau virtuel la réplication HDInsight HBase][img-vnet-diagram]

##<a name="prerequisites"></a>Conditions préalables
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un poste de travail avec Azure PowerShell**.

    Avant d’exécuter des scripts PowerShell, vérifiez que vous êtes connecté à votre abonnement Azure à l’aide de l’applet de commande suivante :

        Add-AzureAccount

    Si vous avez plusieurs abonnements Azure, utilisez l’applet de commande suivante pour définir l’abonnement actif :

        Select-AzureSubscription <AzureSubscriptionName>
        
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Réseau virtuel Azure deux connectivité VPN**.  Pour plus d’informations, voir [configurer une connexion VPN entre deux réseaux virtuels Azure][hdinsight-hbase-geo-replication-vnet].

>[AZURE.NOTE] Noms de service Azure et machine virtuelle doit être unique. Le nom utilisé dans ce didacticiel est Contoso-[nom Azure Service/machine virtuelle]-[UE / États-Unis]. Par exemple, Contoso-VNet-UE est le réseau virtuel Azure dans le centre de données Europe du Nord ; Contoso-DNS-US est le serveur DNS machine virtuelle US Extrême-Orient centres de données. Vous devez parvenir à vos propres noms.
 
 
##<a name="create-azure-virtual-machines-to-be-used-as-dns-servers"></a>Créer des machines virtuelles Azure à utiliser comme serveurs DNS

**Pour créer une machine virtuelle au sein de Contoso-VNet-UE, appelée Contoso-DNS-UE**

1.  Cliquez sur **Nouveau** **calcul**, **MACHINE virtuelle**, **De la galerie**.
2.  Choisissez **Windows Server 2012 R2 centre de données**.
3.  Entrez :
    - **Nom de la MACHINE virtuelle**: Contoso-DNS-UE
    - **Nom du nouvel utilisateur**: 
    - **Nouveau mot de passe**: 
4.  Entrez :
    - **SERVICE CLOUD**: créez un service cloud
    - **Région/groupe affinité/virtuelle réseau**: (sélectionnez Contoso-VNet-Union européenne)
    - **Sous-réseaux virtuelle**: sous-réseau-1
    - **Compte de stockage**: utilisez un compte de stockage générées automatiquement
    
        Le nom du service cloud sera la même que le nom de la machine virtuelle. Dans ce cas, c’est Contoso-DNS-UE. Pour les autres machines virtuelles, puis-je choisir d’utiliser le service cloud même.  Tous les ordinateurs virtuels sous le service cloud même partagent le même réseau virtuel et un suffixe de domaine.

        Le compte de stockage permet de stocker le fichier image machine virtuelle. 
    - **Points de terminaison**: (faites défiler vers le bas et sélectionnez **DNS**) 

Après la création de la machine virtuelle, découvrez interne IP et IP externe.

1.  Cliquez sur le nom de la machine virtuelle, **Contoso-DNS-UE**.
2.  Cliquez sur **tableau de bord**.
3.  Notez :
    - ADRESSE IP VIRTUELLE PUBLIC
    - ADRESSE IP INTERNE


**Pour créer une machine virtuelle au sein de Contoso-VNet-US, appelée Contoso-DNS-US** 

- Répétez cette procédure pour créer une machine virtuelle avec les valeurs suivantes :
    - NOM de la MACHINE virtuelle : Contoso-DNS-US
    - RÉGION/groupe affinité/virtuelle réseau : sélectionnez Contoso-VNET-US
    - RÉSEAU virtuel sous-réseaux : Sous-réseau-1
    - COMPTE de stockage : Utilisez un compte de stockage générées automatiquement
    - Points de terminaison : (sélectionnez DNS)

##<a name="set-static-ip-addresses-for-the-two-virtual-machines"></a>Définir les adresses IP pour les deux machines virtuelles

Serveurs DNS requiert des adresses IP statiques.  Cette étape ne peut pas être effectuée à partir du portail classique Azure. Vous allez utiliser PowerShell Azure.

**Pour configurer l’adresse IP statique pour les deux machines virtuelles**

1. Ouvrez Windows PowerShell ISE.
2. Exécuter les applets de commande.  

        Add-AzureAccount
        Select-AzureSubscription [YourAzureSubscriptionName]
        
        Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
        Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

    NomService est le nom du service cloud. Étant donné que le serveur DNS est la première machine virtuelle du service cloud, le nom du service cloud est identique au nom de machine virtuelle.

    Vous devrez peut-être mettre à jour ServiceName% et nom pour faire correspondre les noms que vous avez.


##<a name="add-the-dns-server-role-the-two-virtual-machines"></a>Ajouter les ordinateurs virtuels de rôle deux serveur DNS

**Pour ajouter le rôle serveur DNS pour Contoso-DNS-EU**

1.  À partir du portail classique Azure, cliquez sur **Machines virtuelles** à gauche. 
2.  Cliquez sur **Contoso-DNS-UE**.
3.  Cliquez sur **tableau de bord** à partir du haut.
4.  Cliquez sur **se connecter** à partir du bas et suivez les instructions pour vous connecter à la machine virtuelle via RDP.
2.  Dans la session RDP, cliquez sur le bouton Windows dans le coin inférieur gauche pour ouvrir l’écran d’accueil.
3.  Cliquez sur la vignette du **Gestionnaire de serveur** .
4.  Cliquez sur **Ajouter des rôles et fonctionnalités**.
5.  Cliquez sur **suivant**
6.  Sélectionnez **l’installation basée sur une fonctionnalité ou rôle**, puis cliquez sur **suivant**.
7.  Sélectionnez votre machine virtuelle DNS (il doit être mis en surbrillance déjà), puis cliquez sur **suivant**.
8.  Vérifier **le serveur DNS**.
9.  Cliquez sur **Ajouter des fonctionnalités**, puis cliquez sur **Continuer**.
10. Cliquez trois fois sur **suivant** , puis cliquez sur **installer**. 

**Pour ajouter le rôle serveur DNS pour Contoso-DNS-US**

- Répétez les étapes pour ajouter le rôle de DNS à **Contoso-DNS-US**.

##<a name="assign-dns-servers-to-the-virtual-networks"></a>Affecter des serveurs DNS pour les réseaux virtuels

**Pour enregistrer les deux serveurs DNS**

1.  À partir du portail classique Azure, cliquez sur **Nouveau**, **SERVICES réseau**, **Réseau virtuel**, **Inscrire un serveur DNS**.
2.  Entrez :
    - **Nom**: Contoso-DNS-UE
    - **Adresse IP du serveur DNS**: 10.1.0.4 – l’adresse IP doit correspondant à l’adresse IP de machine virtuelle du serveur DNS.
     
3.  Répétez le processus pour vous inscrire Contoso-DNS-US avec les paramètres suivants :
    - **Nom**: Contoso-DNS-US
    - **Adresse IP du serveur DNS**: 10.2.0.4

**Pour attribuer les deux serveurs DNS à deux réseaux virtuels**

1.  Dans le volet gauche du portail classique, cliquez sur **les réseaux** .
2.  Cliquez sur **Contoso-VNet-UE**.
3.  Cliquez sur **configurer**.
4.  Sélectionnez **Contoso-DNS-UE** dans la section **dns servers** .
5.  Cliquez sur **Enregistrer** dans la partie inférieure de la page, puis cliquez sur **Oui** pour confirmer.
6.  Répétez le processus pour affecter le serveur DNS **Contoso-DNS-US** au réseau virtuel **Contoso-VNet-US** .

Tous les ordinateurs virtuels qui ont été déployés sur les réseaux virtuels doit être redémarrés pour mettre à jour de la configuration du serveur DNS.

**Pour redémarrer les machines virtuelles**

1. À partir du portail classique Azure, cliquez sur **Machines virtuelles** à gauche.
2. Cliquez sur **Contoso-DNS-UE**.
3. Cliquez sur **tableau de bord** à partir du haut.
4. En bas, cliquez sur **redémarrer** .
5. Répétez ces étapes pour redémarrer **Contoso-DNS-US**.


##<a name="configure-dns-conditional-forwarders"></a>Configurer les redirecteurs DNS conditionnels

Le serveur DNS sur chaque réseau virtuel peut résoudre uniquement les noms DNS ce réseau virtuel. Vous devez configurer un redirecteurs conditionnelle pour qu’il pointe vers le serveur DNS homologue pour la résolution de nom dans le réseau virtuel égal.

Pour configurer redirecteurs conditionnelle, vous devez connaître les suffixes de domaine des deux serveurs DNS. Le suffixe DNS peut être différent en fonction de la configuration des Services de Cloud que vous avez utilisé lorsque vous avez créé les machines virtuelles. Pour chaque suffixe DNS utilisé dans le réseau virtuel, vous devez ajouter un redirecteurs conditionnelle. 

**Pour rechercher les suffixes de domaine des deux serveurs DNS**

1. RDP dans **Contoso-DNS-Union européenne**.
2. Ouvrir la console Windows PowerShell ou invite de commandes.
3. Exécutez **ipconfig**et notez le **suffixe DNS spécifique à la connexion**.
4. Ne fermez pas la session RDP, vous devez toujours dessus. 
5. Répétez ces étapes pour déterminer le **suffixe DNS spécifique à la connexion** de **Contoso-DNS-US**.


**Pour configurer des redirecteurs DNS**
 
1.  À partir de la session RDP à **Contoso-DNS-Union européenne**, cliquez sur la touche Windows dans le coin inférieur gauche.
2.  Cliquez sur **Outils d’administration**.
3.  Cliquez sur **DNS**.
4.  Dans le volet gauche, développez **DSN**, **Contoso-DNS-UE**.
5.  Entrez les informations suivantes :
    - **Domaine DNS**: entrez le suffixe DNS de Contoso-DNS-US. Par exemple : Contoso-DNS-US.b5.internal.cloudapp.net.
    - **Adresses IP des serveurs maîtres**: entrez 10.2.0.4, qui est l’adresse IP de Contoso-DNS-États-Unis.
6.  Appuyez sur **entrée**, puis cliquez sur **OK**.  Maintenant que vous ne pourrez pas résoudre l’adresse IP de Contoso-DNS-États-Unis de Contoso-DNS-Union européenne.
7.  Répétez les étapes pour ajouter un redirecteurs DNS du service DNS sur l’ordinateur virtuel Contoso-DNS-US avec les valeurs suivantes :
    - **Domaine DNS**: entrez le suffixe DNS de l’UE de DNS de Contoso. 
    - **Adresses IP des serveurs maîtres**: entrez 10.2.0.4, qui est l’adresse IP de Contoso-DNS-l’Union européenne.

##<a name="test-the-name-resolution-across-the-virtual-networks"></a>Tester la résolution de noms sur les réseaux virtuels

Vous pouvez à présent tester résolution de noms sur les réseaux virtuels. Ping est bloquée par le pare-feu par défaut.  Vous pouvez utiliser nslookup pour résoudre les machines virtuelles de serveur DNS (vous devez utiliser nom de domaine complet) dans les réseaux égal.  


##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à configurer la résolution de noms sur les réseaux virtuels avec les connexions VPN. Porter sur les deux autres articles de la série :

- [Configurer une connexion VPN entre deux réseaux virtuels Azure][hdinsight-hbase-geo-replication-vnet]
- [Configurer la réplication de geo HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[powershell-install]: powershell-install-configure.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png 
