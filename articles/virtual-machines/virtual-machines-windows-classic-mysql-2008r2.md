<properties
    pageTitle="Créer un ordinateur exécutant MySQL | Microsoft Azure"
    description="Créer une machine virtuelle Azure exécutant Windows Server 2012 R2 et la base de données MySQL à l’aide du modèle de déploiement classique."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="cynthn"/>


# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2012-r2"></a>Installer MySQL sur une machine virtuelle créée avec le modèle de déploiement classique exécutant Windows Server 2012 R2

[MySQL](http://www.mysql.com) est une base de données SQL populaires ouvrir la source. Ce didacticiel montre comment installer et exécuter la version de la Communauté de MySQL 5.6.23 comme un serveur MySQL sur un ordinateur virtuel exécutant Windows Server 2012 R2. Pour obtenir des instructions sur l’installation de MySQL sur Linux, reportez-vous à : [comment installer MySQL sur Azure](virtual-machines-linux-mysql-install.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="create-a-virtual-machine-running-windows-server-2012-r2"></a>Créer une machine virtuelle exécutant Windows Server 2012 R2

Si vous n’avez pas un ordinateur exécutant Windows Server 2012 R2, vous pouvez utiliser ce [didacticiel](virtual-machines-windows-classic-tutorial.md) pour créer la machine virtuelle. 

## <a name="attach-a-data-disk"></a>Joindre un disque de données

Une fois la machine virtuelle est créée, vous pouvez éventuellement joindre un disque de données supplémentaires. Il est recommandé pour les charges de travail et d’éviter de manquer d’espace sur le lecteur système d’exploitation (c), qui inclut le système d’exploitation.

Découvrez [comment joindre un disque de données pour une machine virtuelle Windows](virtual-machines-windows-classic-attach-disk.md) et suivez les instructions pour joindre un disque vide. Définissez le paramètre de cache hôte sur **Aucun** ou **en lecture seule**.

## <a name="log-on-to-the-virtual-machine"></a>Ouvrez une session sur l’ordinateur virtuel

Ensuite, vous allez [Ouvrir une session sur l’ordinateur virtuel](virtual-machines-windows-classic-connect-logon.md) afin que vous puissiez installer MySQL.

##<a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Installer et exécuter le serveur MySQL de la Communauté sur la machine virtuelle

Suivez ces étapes pour installer, configurer et exécuter la version communautaire du serveur MySQL :

> [AZURE.NOTE] Ces étapes sont appliquent à la 5.6.23.0 version communautaire de MySQL et Windows Server 2012 R2. Votre expérience peut-être être différent pour les différentes versions de MySQL ou Windows Server.

1.  Une fois que vous avez connecté à la machine virtuelle à l’aide de bureau à distance, cliquez sur **Internet Explorer** à partir de l’écran d’accueil.
2.  Sélectionnez le bouton **Outils** dans le coin supérieur droit (l’icône roue cogged), puis cliquez sur **Options Internet**. Cliquez sur l’onglet **sécurité** , cliquez sur l’icône **Sites de confiance** , puis cliquez sur le bouton **Sites** . Ajoutez http://*. mysql.com à la liste des sites de confiance. Cliquez sur * *Fermer**, puis cliquez sur * *OK**.
3.  Dans la barre d’adresse de Internet Explorer, tapez http://dev.mysql.com/downloads/mysql/.
4.  Utiliser le site MySQL pour localiser et téléchargez la dernière version de MySQL Installer pour Windows. Lorsque vous choisissez le programme d’installation MySQL, télécharger la version qui comporte l’ensemble des fichiers (par exemple, la mysql-installer-Communauté-5.6.23.0.msi avec une taille de fichier de 282.4 Mo), puis enregistrez le programme d’installation.
5.  Lorsque le programme d’installation téléchargement est terminé, cliquez sur **exécuter** pour lancer le programme d’installation.
6.  Dans la page **Contrat de licence** , acceptez le contrat de licence et cliquez sur **suivant**.
7.  Dans la page **Choisir un Type d’installation** , cliquez sur le type d’installation souhaité, puis cliquez sur **suivant**. Les étapes suivantes impliquent la sélection du type de configuration **serveur uniquement** .
8.  Dans la page **d’Installation** , cliquez sur **exécuter**. Une fois l’installation terminée, cliquez sur **suivant**.
9.  Dans la page **Configuration du produit** , cliquez sur **suivant**.
10. Dans la page **Type et la mise en réseau** , définissez vos options de connectivité et de type configuration souhaitée, y compris le port TCP si nécessaire. Sélectionnez **Afficher les Options avancées**, puis cliquez sur **suivant**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_TypeNetworking.png)

11. Dans la page **comptes et des rôles** , spécifiez un mot de passe fort MySQL racine. Ajouter d’autres comptes d’utilisateur de MySQL selon vos besoins, puis cliquez sur **suivant**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AccountsRoles_Filled.png)

12. Dans la page **Service Windows** , spécifier des modifications concernant les paramètres par défaut pour exécuter le serveur MySQL comme un service Windows selon vos besoins, puis cliquez sur **suivant**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_WindowsService.png)

13. Dans la page **Options avancées** , spécifiez les modifications apportées aux options de journalisation selon vos besoins, puis cliquez sur **suivant**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AdvOptions.png)

14. Dans la page **Configuration du serveur appliquer** , cliquez sur **exécuter**. Lorsque les étapes de configuration sont terminées, cliquez sur **Terminer**.
15. Dans la page **Configuration du produit** , cliquez sur **suivant**.
16. Dans la page **Installation terminée** , cliquez sur **Journal copier dans le Presse-papiers** si vous voulez examiner plus tard, puis cliquez sur **Terminer**.
17. À partir de l’écran Démarrer, tapez **mysql**, puis cliquez sur **MySQL 5.6 ligne de commande Client**.
18. Entrez le mot de passe racine que vous avez spécifiée à l’étape 11 et s’affiche avec une invite de commandes où vous pouvez envoyer des commandes pour configurer MySQL. Pour obtenir des informations de commandes et de syntaxe, voir [Manuels de référence MySQL](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_CommandPrompt.png)

19. Vous pouvez également configurer les paramètres par défaut configuration serveur, tels que les lecteurs et des répertoires de base et des données avec des entrées dans le fichier C:\Program Files (x86) \MySQL\MySQL Server 5.6\my-default.ini. Pour plus d’informations, voir [5.1.2 Configuration du serveur par défaut est](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Configurer les points de terminaison

Si vous souhaitez que le service serveur MySQL soit disponible pour les ordinateurs client MySQL sur Internet, vous devez configurer un point de terminaison pour le port TCP sur lequel le service serveur MySQL écoute et créer une règle de pare-feu Windows supplémentaire. Il s’agit de ports TCP 3306 sauf si vous spécifiez un port différent dans la page **Type et mise en réseau** (étape 10 de la procédure précédente).


> [AZURE.NOTE] Vous envisagez avec soin les implications de sécurité d’y parvenir, car cela facilitera le service serveur MySQL disponibles à tous les ordinateurs sur Internet. Vous pouvez définir l’ensemble des adresses IP source qui sont autorisés à utiliser le point de terminaison avec une contrôle liste (accès). Pour plus d’informations, voir [comment définir des points de terminaison pour une Machine virtuelle](virtual-machines-windows-classic-setup-endpoints.md).


Pour configurer un point de terminaison du service serveur MySQL :

1.  Dans le portail Azure classique, cliquez sur **Machines virtuelles**, cliquez sur le nom de votre machine virtuelle MySQL, puis sur les **points de terminaison**.
2.  Dans la barre de commandes, cliquez sur **Ajouter**.
3.  Dans la page **Ajouter un point de terminaison à une machine virtuelle** , cliquez sur la flèche droite.
4.  Si vous utilisez la valeur par défaut le port MySQL TCP 3306, cliquez sur **MySQL** dans la zone **nom**, puis cliquez sur la coche.
5.  Si vous utilisez un port TCP différent, tapez un nom unique dans la zone **nom**. Sélectionnez **TCP** dans protocole, tapez le numéro de port dans **Port Public** et **Privé Port**, puis cliquez sur la coche.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Ajouter une règle de pare-feu Windows pour autoriser le trafic MySQL

Pour ajouter une règle de pare-feu Windows qui permet du trafic MySQL à partir d’Internet, exécutez la commande suivante à une invite de commande Windows PowerShell avec élévation de privilèges sur la serveur MySQL les machine virtuelle.

    New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public


    
## <a name="test-your-remote-connection"></a>Tester votre connexion à distance


Pour tester votre connexion au service serveur MySQL en cours d’exécution sur l’ordinateur virtuel Azure à distance, vous devez d’abord déterminer le nom DNS correspondant au service de nuage qui contient la machine virtuelle en cours d’exécution serveur MySQL.

1.  Dans le portail Azure classique, cliquez sur **Machines virtuelles**et cliquez sur le nom de votre serveur de machine virtuelle MySQL, puis cliquez sur **tableau de bord**.
2.  Dans le tableau de bord machine virtuelle, notez la valeur **Nom DNS** sous la section **Aperçu rapide** . Voici un exemple :

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_DNSName.png)

3.  À partir d’un ordinateur local exécutant MySQL ou le client MySQL, exécutez la commande suivante pour vous connecter tant qu’utilisateur MySQL.

        mysql -u <yourMysqlUsername> -p -h <yourDNSname>

    Par exemple, pour le dbadmin3 de nom d’utilisateur MySQL et le nom DNS testmysql.cloudapp.net la machine virtuelle, utilisez la commande suivante.

        mysql -u dbadmin3 -p -h testmysql.cloudapp.net


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’exécution de MySQL, consultez la [Documentation MySQL](http://dev.mysql.com/doc/).
