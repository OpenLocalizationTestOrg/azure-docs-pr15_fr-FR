<properties
    pageTitle="Considérations relatives à la sécurité pour SQL Server dans Azure | Microsoft Azure"
    description="Cette rubrique fait référence aux ressources créées avec le modèle de déploiement classique et fournit des instructions générales pour la sécurisation de SQL Server dans une Machine virtuelle Azure en cours d’exécution."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
   editor=""    
   tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="06/24/2016"
    ms.author="jroth" />

# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considérations relatives à la sécurité pour SQL Server dans des Machines virtuelles Azure
 
Cette rubrique comprend des instructions de sécurité générale qui aident à établir un accès sécurisé aux instances de SQL Server dans une machine virtuelle Azure. Toutefois, afin d’assurer une meilleure protection pour vos instances de base de données SQL Server dans Azure, nous vous recommandons de mettre en œuvre les pratiques de sécurité traditionnel local outre les meilleures pratiques de sécurité pour Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Pour plus d’informations sur les pratiques de sécurité SQL Server, voir [SQL Server 2008 R2 meilleures pratiques de sécurité - opérationnels et les tâches administratives](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure est conforme à plusieurs réglementations et normes qui vous permet de créer une solution compatible avec SQL Server s’exécutant sur une Machine virtuelle. Pour plus d’informations sur la conformité réglementaire avec Azure, voir le [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/support/trust-center/).

Voici une liste de recommandations de sécurité à prendre en compte lors de la configuration et la connexion à l’instance de SQL Server dans une machine virtuelle Azure.

## <a name="considerations-for-managing-accounts"></a>Considérations pour la gestion des comptes :

- Créer un compte d’administrateur local unique qui n’est pas nommé **administrateur**.

- Utilisez des mots de passe forts complexes pour tous vos comptes. Pour plus d’informations sur la création d’un mot de passe, voir l’article de [conseils pour la création d’un mot de passe fort](http://windows.microsoft.com/en-us/windows-vista/Tips-for-creating-a-strong-password) .

- Par défaut, Azure sélectionne l’authentification Windows pendant l’installation de la Machine virtuelle de SQL Server. Par conséquent, la connexion **SA** est désactivée et un mot de passe est affecté par le programme d’installation. Nous recommander la connexion **SA** doit être n'être pas utilisée ou activée. Voici des stratégies de remplacement si vous souhaitez une connexion SQL :
    - Créer un compte SQL disposant d’un membre sysadmin.
    - Si vous devez utiliser une connexion **SA** , activer la connexion et renommez-le et affecter un nouveau mot de passe.
    - Les deux options qui ont été décrites précédemment nécessitent une modification du mode d’authentification pour **SQL Server et Mode d’authentification Windows**. Pour plus d’informations, voir [Changer le Mode d’authentification serveur](https://msdn.microsoft.com/library/ms188670.aspx).

## <a name="considerations-for-securing-connections-to-azure-virtual-machine"></a>Considérations pour sécuriser les connexions à Azure Machine virtuelle :

- Envisagez d’utiliser le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) pour administrer les machines virtuelles au lieu de ports RDP publics.

- Utiliser un [Groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) (NSG) pour autoriser ou refuser le trafic réseau de votre ordinateur virtuel. Si vous voulez utiliser un NSG et un point de terminaison et déjà en place, commencez par supprimez le point de terminaison et. Pour plus d’informations sur la façon de procéder, voir [Gestion des accès aux listes de contrôle () pour les points de terminaison à l’aide de PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

- Si vous utilisez des points de terminaison, supprimez les points de terminaison de la machine virtuelle si vous n’utilisez pas les. Pour obtenir des instructions sur l’utilisation des utilisateurs avec points de terminaison, voir [gérer l’et sur un point de terminaison](../virtual-network/virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

- Activer une option de connexion chiffrée suivant d’une instance du moteur de base de données SQL Server dans le Machines virtuelles Azure. Configurer une instance SQL server avec un certificat signé. Pour plus d’informations, voir [Activer des connexions chiffrées au moteur de base de données](https://msdn.microsoft.com/library/ms191192.aspx) et la [Syntaxe de la chaîne de connexion](https://msdn.microsoft.com/library/ms254500.aspx).

- Si vos machines virtuelles doivent être accessibles uniquement à partir d’un réseau spécifique, utilisez le pare-feu Windows pour limiter l’accès à certaines adresses IP ou sous-réseaux.

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes également intéressé meilleures pratiques concernant les performances, voir [Les procédures recommandées pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-performance.md).

D’autres rubriques liés à l’exécution de SQL Server dans Azure machines virtuelles, consultez [SQL Server dans la vue d’ensemble des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
