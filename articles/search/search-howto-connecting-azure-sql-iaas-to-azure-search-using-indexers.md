<properties 
    pageTitle="Configurer une connexion à partir d’un indexeur recherche Azure à SQL Server sur un ordinateur virtuel Azure | Microsoft Azure | Indexeurs" 
    description="Activer des connexions chiffrées et configurer le pare-feu pour autoriser les connexions à SQL Server sur un ordinateur virtuel Azure (machine virtuelle) à partir d’un indexeur recherche Azure." 
    services="search" 
    documentationCenter="" 
    authors="jack4it" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="09/26/2016" 
    ms.author="jackma"/>

# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurer une connexion à partir d’un indexeur recherche Azure à SQL Server sur un ordinateur virtuel Azure

Comme indiqué dans [Connexion Azure base de données SQL Azure recherche à l’aide d’indexeurs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions), création indexeurs par rapport à **SQL Server sur machines virtuelles Azure** (ou **Machines virtuelles SQL Azure** en abrégé) est prise en charge par la recherche Azure, mais il existe quelques conditions préalables liés à la sécurité pour prendre en charge de la première. 

**Durée de la tâche :** Environ 30 minutes, en supposant que vous déjà installé un certificat sur l’ordinateur virtuel.

## <a name="enable-encrypted-connections"></a>Activer des connexions chiffrées

Recherche Azure exige un canal chiffré pour toutes les demandes indexeur via une connexion internet public. Cette section répertorie les étapes pour y parvenir.

1. Vérifiez les propriétés du certificat pour vérifier que le nom de sujet est le nom de domaine complet (FQDN) de la machine virtuelle Azure. Vous pouvez utiliser un outil comme CertUtils ou le composant logiciel enfichable Certificats pour afficher les propriétés. Vous pouvez obtenir le nom de domaine complet à partir de la section Essentials de la carte service machine virtuelle, dans le champ **adresse IP Public/DNS Nom étiquette** , dans le [portail Azure](https://portal.azure.com/).

    - Pour les machines virtuelles créées à l’aide de la plus récent modèle **Gestionnaire de ressources** , le nom de domaine complet est mis en forme en tant que `<your-VM-name>.<region>.cloudapp.azure.com`. 

    - Pour machines virtuelles plus anciens comme une **classique** machine virtuelle, le nom de domaine complet est mis en forme en tant que `<your-cloud-service-name.cloudapp.net>`. 

2. Configurer SQL Server pour utiliser le certificat à l’aide de l’Éditeur du Registre (regedit). 

    Bien que le Gestionnaire de Configuration SQL Server sont souvent utilisé pour cette tâche, vous ne pouvez pas l’utiliser pour ce scénario. Elle ne trouve pas le certificat importé, car le nom de domaine complet de la machine virtuelle sur Azure ne correspondent pas au nom de domaine complet que déterminée par la machine virtuelle (elle identifie le domaine en tant que l’ordinateur local ou le domaine réseau auquel il est joint). Lorsque les noms ne correspondent pas, utilisez regedit pour spécifier le certificat.

    - Dans regedit, accédez à la clé de Registre suivante : `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
    La `[MSSQL13.MSSQLSERVER]` composant varie en fonction du nom de version et d’instance. 

    - Définissez la valeur de la clé de **certificat** à l' **empreinte numérique** du certificat SSL que vous avez importé de la machine virtuelle.

    Il existe plusieurs méthodes pour obtenir l’empreinte numérique, certains mieux que d’autres personnes. Si vous le copiez dans le composant logiciel enfichable **certificats** dans la console MMC, vous sera probablement décrochez un invisible les zéros non caractère [comme décrit dans cet article du support technique](https://support.microsoft.com/kb/2023869/), qui se traduit par un message d’erreur lorsque vous essayez d’une connexion. Il existe plusieurs solutions de contournement pour résoudre ce problème. Le plus simple consiste à revenir en arrière sur, puis retapez le premier caractère de l’empreinte numérique pour supprimer le caractère à gauche dans le champ de valeur de clé dans regedit. Par ailleurs, vous pouvez utiliser un autre outil pour copier l’empreinte numérique.

3. Accorder des autorisations pour le compte de service. 

    Vérifiez que le compte de service SQL Server est l’autorisation appropriée sur la clé privée du certificat SSL. Si vous ignorez cette étape, SQL Server ne démarre pas. Vous pouvez utiliser le composant logiciel enfichable **certificats** ou **CertUtils** pour cette tâche.

4. Redémarrez le service SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurer la connectivité de SQL Server dans la machine virtuelle

Après avoir configuré la connexion cryptée requise par Search Azure, étapes de configuration supplémentaires sont intrinsèque à SQL Server sur machines virtuelles Azure. Si vous n’avez pas déjà fait, l’étape suivante consiste pour terminer la configuration à l’aide de l’un des articles suivants :

- Pour un **Gestionnaire de ressources** machine virtuelle, voir [se connecter à un ordinateur de virtuelle SQL Server dans Azure à l’aide du Gestionnaire de ressources](../virtual-machines/virtual-machines-windows-sql-connect.md). 

- Pour un **classique** machine virtuelle, voir [se connecter à un ordinateur de virtuelle SQL Server dans Azure classique](../virtual-machines/virtual-machines-windows-classic-sql-connect.md).

En particulier, consultez la section de chaque article de « connexion via internet ».

## <a name="configure-the-network-security-group-nsg"></a>Configurer le groupe de sécurité réseau (NSG)

Il n’est pas inhabituelle à configurer la NSG et un point de terminaison Azure correspondant ou un contrôle de liste d’accès pour rendre votre machine virtuelle Azure accessible à d’autres parties. Sans doute que vous avez défini ces avant pour permettre à votre propre logique de l’application pour vous connecter à votre ordinateur virtuel Azure SQL. Il n’est pas différente de connexion à votre machine virtuelle Azure SQL Azure recherche. 

Les liens ci-dessous fournissent des instructions sur la configuration NSG pour les déploiements machine virtuelle. Utilisez ces instructions pour et un point de terminaison Azure recherche basée sur son adresse IP.

> [AZURE.NOTE] Pour l’arrière-plan, consultez [qu’est un groupe de sécurité réseau ?](../virtual-network/virtual-networks-nsg.md)

- Pour un **Gestionnaire de ressources** machine virtuelle, Découvrez [comment créer NSGs pour les déploiements processeur](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

- Pour un **classique** machine virtuelle, Découvrez [comment créer NSGs pour les déploiements classique](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Adresses IP peut poser quelques défis auxquels sont facilement résoudre si vous connaissez l’existence du problème et des solutions de contournement potentielles. Les sections restantes fournissent des recommandations pour la gestion des problèmes liés aux adresses IP dans l’et.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Limiter l’accès à l’adresse IP du service recherche

Nous vous recommandons vivement de limiter l’accès à l’adresse IP du service de recherche dans l’et au lieu de rendre vos ordinateurs virtuels Azure SQL pleins à des demandes de connexion. Vous pouvez facilement déterminer l’adresse IP en exécutant la commande ping le nom de domaine complet (par exemple, `<your-search-service-name>.search.windows.net`) de votre service de recherche.

#### <a name="managing-ip-address-fluctuations"></a>Gestion des variations d’adresse IP

Si votre service de recherche n'a qu’une seule unité de recherche (autrement dit, un réplica et une partition), l’adresse IP changera lors d’un entretien de routine redémarrage, invalider un existant et l’adresse IP de votre service de recherche.

Pour éviter l’erreur suivante connectivité consiste à utiliser plusieurs réplica et une partition dans Azure rechercher. Cela permet d’augmenter le coût, mais il permet également de résoudre le problème d’adresse IP. Dans Rechercher Azure, adresses IP ne changent pas lorsque vous avez plus d’une unité de recherche.

Un deuxième consiste à autoriser la connexion à échouer et puis reconfigurer les utilisateurs dans le NSG. En moyenne, vous pouvez vous attendre adresses IP pour modifier toutes les semaines. Pour les clients qui achètent l’indexation contrôlé irrégulier, cette approche peut être viable.

Une troisième approche viable (mais pas particulièrement sécurisée) consiste à spécifier la plage d’adresses IP de la région Azure où votre service de recherche est mis en service. La liste des plages d’adresses IP à partir de laquelle les adresses IP publiques sont affectés à des ressources Azure est publiée en [plages d’adresses IP Azure centre de données](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Inclure les adresses IP portails recherche Azure

Si vous utilisez le portail Azure pour créer un indexeur, logique portail Azure recherche doit également accéder à votre machine virtuelle Azure SQL pendant la création. Adresses IP portails recherche Azure soient retrouvés en exécutant la commande ping `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Étapes suivantes

Avec de la configuration, vous pouvez maintenant spécifier un serveur SQL Server sur machine virtuelle Azure comme source de données pour un indexeur recherche Azure. Pour plus d’informations, voir [Se connecter Azure base de données SQL Azure recherche à l’aide d’indexeurs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) .
