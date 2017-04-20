<properties
    pageTitle="S’appliquent-elles local toujours sur les groupes de disponibilité Azure | Microsoft Azure"
    description="Ce didacticiel utilise les ressources créées avec le modèle de déploiement classique et explique comment utiliser l’Assistant Ajouter réplica dans SQL Server Management Studio (SSMS) pour ajouter un réplica toujours dans le groupe de disponibilité dans Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>S’appliquent-elles local toujours sur les groupes de disponibilité Azure

Toujours sur les groupes de disponibilité une haute disponibilité des groupes de base de données en ajoutant des réplicas secondaires. Celles-ci permettre basculement de bases de données en cas d’échec. Ils permettent en outre décharger lecture charges de travail ou les tâches de sauvegarde.

Vous pouvez étendre les groupes de disponibilité en local à Microsoft Azure en mise en service un ou plusieurs ordinateurs de Azure virtuels avec SQL Server et les ajouter en tant que réplicas à vos groupes de disponibilité en local.

Ce didacticiel suppose que vous disposez des éléments suivants :

- Un abonnement Azure actif. Vous pouvez [vous inscrire une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- Un toujours sur Disponibilité groupe existant en local. Pour plus d’informations sur les groupes de disponibilité, voir [Toujours sur les groupes de disponibilité](https://msdn.microsoft.com/library/hh510230.aspx).

- Connectivité entre le réseau local et votre réseau virtuel Azure. Pour plus d’informations sur la création de ce réseau virtuel, consultez [configurer un réseau privé virtuel de Site à Site dans le portail classique Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="add-azure-replica-wizard"></a>Ajouter Assistant réplica Azure

Cette section vous montre comment utiliser l' **Assistant Ajout de réplica Azure** pour étendre votre solution toujours dans le groupe de disponibilité pour inclure réplicas Azure.

1. À partir de SQL Server Management Studio, développez **Toujours sur haute disponibilité** > **Groupes de disponibilité** > **[nom de votre groupe de disponibilité]**.

1. Droit **Réplicas de disponibilité**, puis cliquez sur **Ajouter un réplica**.

1. Par défaut, le **Ajouter réplica disponibilité groupe Assistant** s’affiche. Cliquez sur **suivant**.  Si vous avez sélectionné l’option **ne pas afficher cette page à nouveau** dans la partie inférieure de la page pendant un menu de lancement précédent de l’Assistant, cet écran ne s’affichera pas.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)

1. Vous devez vous connecter à tous les réplicas secondaires existants. Vous pouvez cliquer sur **Connect...** en regard de chaque réplica ou vous pouvez cliquer sur **Se connecter tout...** en bas de l’écran. Après l’authentification, cliquez sur **suivant** pour passer à l’écran suivant.

1. Dans la page **Spécifier réplicas** , plusieurs onglets apparaissent dans la partie supérieure : **réplicas**, les **points de terminaison**, **Préférences de sauvegarde**et **récepteur**. Sous l’onglet **réplicas** , cliquez sur **Ajouter un réplica Azure...** Pour lancer l’Assistant Ajout de réplica Azure.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)

1. Sélectionnez un certificat de gestion Azure existant dans le magasin de certificats Windows local si vous avez installé avant. Sélectionnez ou entrez l’id d’un abonnement Azure si vous avez utilisé une avant. Vous pouvez cliquer sur Télécharger pour télécharger et installer un certificat de gestion Azure et télécharger la liste des abonnements à l’aide d’un compte Azure.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)

1. Vous allez renseigner chaque champ dans la page dont la valeur doit être utilisé pour créer la Azure Machine virtuelle (machine virtuelle) hébergeant le réplica.

  	|Paramètre|Description|
|---|---|
|**Image**|Sélectionnez la combinaison souhaitée de système d’exploitation et SQL Server|
|**Taille de mémoire virtuelle**|Sélectionnez la taille de la machine virtuelle qui correspond le mieux à vos besoins professionnels|
|**Nom de la machine virtuelle**|Spécifiez un nom unique pour la nouvelle machine virtuelle. Le nom doit contenir entre 3 et 15 caractères, peut contenir uniquement des lettres, des chiffres et des traits d’union et doit commencer par une lettre et se terminent par une lettre ou un nombre.|
|**Nom d’utilisateur machine virtuelle**|Spécifier un nom d’utilisateur qui sera utilisée comme le compte d’administrateur sur l’ordinateur virtuel|
|**Mot de passe administrateur machine virtuelle**|Spécifier un mot de passe pour le nouveau compte|
|**Confirmez le mot de passe**|Confirmez le mot de passe du nouveau compte|
|**Réseau virtuel**|Spécifiez le réseau virtuel Azure que la nouvelle machine virtuelle doit utiliser. Pour plus d’informations sur les réseaux virtuels, voir [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md).|
|**Réseau virtuel sous-réseau**|Spécifie le sous réseau réseau virtuel que la nouvelle machine virtuelle doit utiliser.|
|**Domaine**|Vérifiez que la valeur prédéfinie pour le domaine est correcte|
|**Nom d’utilisateur de domaine**|Spécifier un compte qui se trouve dans le groupe Administrateurs local sur les nœuds du cluster local|
|**Mot de passe**|Spécifier le mot de passe pour le nom d’utilisateur de domaine|

1. Cliquez sur **OK** pour valider les paramètres de déploiement.

1. Conditions juridiques apparaissent en dessous. Lisez et cliquez sur **OK** si vous acceptez les termes.

1. La page **Spécifier réplicas** s’affiche à nouveau. Vérifiez les paramètres pour le nouveau réplica Azure sous les onglets **réplicas**, les **points de terminaison**et les **Préférences de sauvegarde** . Modifier les paramètres pour répondre aux besoins de votre entreprise.  Pour plus d’informations sur les paramètres de contenu sur les onglets suivants, voir [Spécifier la Page de réplicas (disponibilité Assistant Nouveau groupe Assistant/Ajouter réplica)](https://msdn.microsoft.com/library/hh213088.aspx). Notez que les récepteurs ne peut pas être créés à l’aide de l’onglet récepteur pour les groupes de disponibilité qui contiennent des réplicas Azure. En outre, si un récepteur a déjà été créé avant de lancer l’Assistant, vous recevrez un message indiquant qu’il n’est pas pris en charge dans Azure. Nous allons étudier comment créer des récepteurs dans la section **créer un groupe de disponibilité qu’auditeur** .

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)

1. Cliquez sur **suivant**.

1. Sélectionnez la méthode de synchronisation de données que vous voulez utiliser dans la page **Sélectionner la synchronisation initiale des données** , cliquez sur **suivant**. La plupart des scénarios, activez **La synchronisation des données complète**. Pour plus d’informations sur les méthodes de synchronisation de données, voir [Sélectionner données synchronisation Page initiale (toujours sur Disponibilité groupe Assistants)](https://msdn.microsoft.com/library/hh231021.aspx).

1. Passez en revue les résultats dans la page **Validation** . Corriger les problèmes en suspens et exécutez à nouveau le contrôle si nécessaire. Cliquez sur **suivant**.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)

1. Vérifiez les paramètres de la page de **Résumé** , puis cliquez sur **Terminer**.

1. Le processus de mise à disponibilité commence. Une fois l’Assistant terminée, cliquez sur **Fermer** pour quitter l’Assistant.

>[AZURE.NOTE] L’Assistant Ajout de réplica Azure crée un fichier journal dans Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Ce fichier journal peut être utilisé pour résoudre les déploiements réplica Azure a échoué. Si l’Assistant d’échec de l’exécution d’une action, toutes les opérations précédentes sont annulées, y compris la suppression de la machine virtuelle mis en service.

## <a name="create-an-availability-group-listener"></a>Créer un récepteur de groupe de disponibilité

Une fois que le groupe de disponibilité a été créé, vous devez créer un récepteur pour les clients à se connecter aux réplicas. Récepteurs dirigent les connexions entrantes vers le serveur principal ou un réplica secondaire en lecture seule. Pour plus d’informations sur les récepteurs, consultez [configurer un récepteur ILB pour toujours sur les groupes de disponibilité dans Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

## <a name="next-steps"></a>Étapes suivantes

Outre l’utilisation de l' **Assistant Ajout de réplica Azure** pour étendre votre toujours dans le groupe de disponibilité vers Azure, vous pouvez également déplacer certaines charges de travail SQL Server complètement vers Azure. Pour commencer, voir [mise en service d’un serveur SQL Machine virtuelle sur Azure](virtual-machines-windows-portal-sql-server-provision.md).

D’autres rubriques liés à l’exécution de SQL Server dans Azure machines virtuelles, consultez [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).
