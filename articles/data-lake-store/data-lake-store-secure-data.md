<properties 
   pageTitle="Sécuriser les données stockées dans Azure données Lake Store | Microsoft Azure" 
   description="Découvrez comment sécuriser les données Azure données Lake Store à l’aide de groupes et listes de contrôle d’accès" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/29/2016"
   ms.author="nitinme"/>

# <a name="securing-data-stored-in-azure-data-lake-store"></a>Protection des données stockées dans le magasin de Lake données Azure

Protection des données Azure données Lake Store est une approche en trois étapes.

1. Commencez par créer des groupes de sécurité dans Azure Active Directory (DAS). Ces groupes de sécurité sont utilisés pour implémenter l’accès par rôle contrôle d’Azure portail. Pour plus d’informations, voir [Contrôle d’accès basé sur un rôle dans Microsoft Azure](../active-directory/role-based-access-control-configure.md).

2. Affecter les groupes de sécurité AAD au compte Azure données Lake Store. Contrôle l’accès au compte opérations portail et la gestion à partir du portail ou API de données Lake Store.

3. Affecter les groupes de sécurité AAD en tant qu’accès aux listes de contrôle () sur le système de fichiers de données Lake Store.

4. En outre, vous pouvez également définir une plage d’adresses IP pour les clients qui peuvent accéder aux données dans les données Lake Store.

Cet article fournit des instructions sur la façon d’utiliser le portail Azure pour effectuer les tâches ci-dessus. Pour obtenir des informations détaillées sur comment données Lake Store met en œuvre la sécurité au niveau du compte et les données, voir [sécurité Azure données Lake Store](data-lake-store-security-overview.md). Pour informations approfondi comment les utilisateurs sont implémentées dans Azure données Lake Store, voir [Vue d’ensemble du contrôle d’accès données Lake Store](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- **Magasin de Lake de données Azure un compte**. Pour savoir comment en créer une, voir [prise en main Azure données Lake Store](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Créer des groupes de sécurité dans Azure Active Directory

Pour obtenir des instructions sur la façon de créer des groupes de sécurité AAD et comment ajouter des utilisateurs au groupe, voir [Gestion des groupes de sécurité dans Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Affecter des utilisateurs ou groupes de sécurité aux comptes Azure données Lake Store

Lorsque vous affectez des utilisateurs ou des groupes de sécurité aux comptes Azure données Lake Store, vous contrôlez l’accès à des opérations de gestion sur le compte à l’aide du portail Azure et API du Gestionnaire de ressources Azure. 

1. Ouvrir un compte Azure données Lake Store. Dans le volet gauche, cliquez sur **Parcourir**et cliquez sur **Données Lake Store**, puis à partir de la carte de données Lake Store, cliquez sur le nom du compte auquel vous voulez affecter un utilisateur ou groupe de sécurité.

2. Dans votre carte de compte données Lake Store, cliquez sur **paramètres**. À partir de la carte de **paramètres** , cliquez sur **utilisateurs**.

    ![Affecter le groupe de sécurité à compte Azure données Lake Store] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Affecter le groupe de sécurité à compte Azure données Lake Store")

3. La carte **utilisateur** par défaut répertorie le groupe **administrateurs de l’abonnement** en tant que propriétaire. 

    ![Ajouter des utilisateurs et les rôles] (./media/data-lake-store-secure-data/adl.add.group.roles.png "Ajouter des utilisateurs et les rôles")
 
    Il existe deux façons d’ajouter un groupe et affecter des rôles appropriés.

    * Ajouter un utilisateur/groupe au compte et puis attribuer un rôle, ou
    * Ajouter un rôle, puis affectez-leur des groupes d’utilisateurs au rôle.

    Dans cette section, nous examinons la première approche, ajout d’un groupe et puis attribution de rôles. Vous pouvez effectuer une procédure similaire pour un rôle d’abord sélectionner, puis affectez-leur des groupes à ce rôle.
    
4. Dans la carte **utilisateurs** , cliquez sur **Ajouter** pour ouvrir la carte **Ajouter access** . Dans la carte **Ajouter access** , cliquez sur **Sélectionner un rôle**et sélectionnez un rôle pour l’utilisateur ou du groupe.

     ![Ajout d’un rôle pour l’utilisateur] (./media/data-lake-store-secure-data/adl.add.user.1.png "Ajout d’un rôle pour l’utilisateur")

    Le **propriétaire** et le rôle de **collaborateur** donnent accès à un éventail de fonctions d’administration sur le compte lake de données. Pour les utilisateurs qui interagiront avec les données dans le lake de données, vous pouvez les ajouter au rôle de **lecteur **. L’étendue de ces rôles est limitée aux opérations de gestion du associés au compte Azure données Lake Store.

    Pour les données autorisations du système de fichiers individuels opérations définissent que les utilisateurs peuvent faire. Par conséquent, un utilisateur ayant un rôle Lecteur peut uniquement afficher les paramètres d’administration associés au compte, mais pouvez potentiellement lire et écrire des données en fonction des autorisations de système de fichiers qui leur sont affectées. Autorisations de système de fichiers de données Lake Store sont décrites à [affecter le groupe de sécurité comme utilisateurs au système de fichiers Azure données Lake Store](#filepermissions).



5. Dans la carte **Ajouter access** , cliquez sur **Ajouter des utilisateurs** pour ouvrir la carte **d’Ajouter des utilisateurs** . Dans cette carte, recherchez le groupe de sécurité que vous avez créée précédemment dans Azure Active Directory. Si vous avez un grand nombre de groupes de recherche à partir de, utilisez la zone de texte dans la partie supérieure pour filtrer sur le nom du groupe. Cliquez sur **Sélectionner**.

    ![Ajouter un groupe de sécurité] (./media/data-lake-store-secure-data/adl.add.user.2.png "Ajouter un groupe de sécurité")

    Si vous voulez ajouter un utilisateur/groupe qui n’est pas répertorié, vous pouvez les inviter en utilisant l’icône **inviter** et en spécifiant l’adresse de messagerie pour l’utilisateur ou du groupe.

6. Cliquez sur **OK**. Vous devriez voir le groupe de sécurité ajouté comme illustré ci-dessous.

    ![Groupe de sécurité ajouté] (./media/data-lake-store-secure-data/adl.add.user.3.png "Groupe de sécurité ajouté")

7. Votre groupe de sécurité de l’utilisateur/a maintenant accès au compte Azure données Lake Store. Si vous voulez fournir un accès à des utilisateurs spécifiques, vous pouvez les ajouter au groupe de sécurité. De même, si vous souhaitez révoquer l’accès d’un utilisateur, vous pouvez les supprimer du groupe de sécurité. Vous pouvez également affecter plusieurs groupes de sécurité à un compte. 

## <a name="filepermissions"></a>Affecter des utilisateurs ou groupe de sécurité comme utilisateurs au système de fichiers Azure données Lake Store

En affectant des groupes de sécurité des utilisateurs au système de fichiers Lake de données Azure, vous définissez contrôle d’accès sur les données stockées dans Azure données Lake sécurisée.

1. Dans votre carte de compte données Lake Store, cliquez sur **Explorateur de données**.

    ![Créer les répertoires dans compte données Lake] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Créer les répertoires dans un compte Lake de données")

2. Dans la carte de **L’Explorateur de données** , cliquez sur le fichier ou dossier pour lequel vous voulez configurer l’et, puis cliquez sur **Access**. Pour attribuer et à un fichier, vous devez cliquer sur **accès** à partir de la carte **d’Aperçu du fichier** .

    ![Définir les utilisateurs sur le système de fichiers de données Lake] (./media/data-lake-store-secure-data/adl.acl.1.png "Définir les utilisateurs sur le système de fichiers de données Lake")

3. La carte **accès** répertorie l’accès standard et personnalisé de déjà affectées à la racine. Cliquez sur l’icône **Ajouter** pour ajouter des utilisateurs de personnaliser le niveau.

    ![Accès à la liste standard et personnalisé] (./media/data-lake-store-secure-data/adl.acl.2.png "Accès à la liste standard et personnalisé")

    * **Accéder à standard** est le niveau d’accès style UNIX, dans laquelle vous spécifiez lire, d’écriture et d’exécution (rwx) à trois catégories d’utilisateurs distincts : propriétaire, groupe et autres personnes.
    * **Access personnalisée** correspond à l’utilisateurs POSIX qui vous permet de définir des autorisations pour des utilisateurs spécifiques désignés ou groupes et pas seulement le fichier propriétaire ou un groupe. 
    
    Pour plus d’informations, voir [HADOOP utilisateurs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Pour plus d’informations sur comment les utilisateurs sont implémentées données Lake Store, consultez [Contrôle d’accès données Lake Store](data-lake-store-access-control.md).

4. Cliquez sur l’icône **Ajouter** pour ouvrir la carte **D’ajout d’accès personnalisés** . Dans cette carte, cliquez sur **Sélectionner un utilisateur ou groupe**et puis dans la carte de **Sélectionner un utilisateur ou groupe** , recherchez le groupe de sécurité que vous avez créée précédemment dans Azure Active Directory. Si vous avez un grand nombre de groupes de recherche à partir de, utilisez la zone de texte dans la partie supérieure pour filtrer sur le nom du groupe. Cliquez sur le groupe que vous voulez ajouter, puis cliquez sur **Sélectionner**.

    ![Ajouter un groupe] (./media/data-lake-store-secure-data/adl.acl.3.png "Ajouter un groupe")

5. Cliquez sur **Sélectionner les autorisations**, sélectionnez les autorisations et si vous voulez affecter les autorisations par défaut et, accéder et ou les deux. Cliquez sur **OK**.

    ![Attribuer des autorisations au groupe] (./media/data-lake-store-secure-data/adl.acl.4.png "Attribuer des autorisations au groupe")

    Pour plus d’informations sur les autorisations dans les données Lake Store et utilisateurs/accès par défaut, voir [Contrôle d’accès données Lake Store](data-lake-store-access-control.md).


6. Dans la carte **D’ajout d’accès personnalisés** , cliquez sur **OK**. Le groupe nouvellement ajouté, avec les autorisations associées, sera désormais répertorié dans la carte **d’accès** .

    ![Attribuer des autorisations au groupe] (./media/data-lake-store-secure-data/adl.acl.5.png "Attribuer des autorisations au groupe")

    > [AZURE.IMPORTANT] Dans la version actuelle, vous ne pouvez avoir 9 entrées sous **Accès personnalisé**. Si vous voulez ajouter des utilisateurs plus de 9, vous devez créer des groupes de sécurité, ajouter des utilisateurs aux groupes de sécurité, ajoutez fournir un accès à ces groupes de sécurité du compte de données Lake Store.

7. Si nécessaire, vous pouvez également modifier les autorisations d’accès après avoir ajouté le groupe. Activez ou désactivez la case à cocher pour chaque type d’autorisation (lecture, écriture, Execute) en fonction selon que vous voulez supprimer ou affecter cette autorisation au groupe de sécurité. Cliquez sur **Enregistrer** pour enregistrer les modifications ou **Ignorer** pour annuler les modifications.

## <a name="set-ip-address-range-for-data-access"></a>Définir la plage d’adresses IP pour accéder aux données

Magasin de Lake données Azure permet de mieux verrouiller l’accès à votre magasin de données au niveau du réseau. Vous pouvez activer le pare-feu, spécifiez une adresse IP ou définir une plage d’adresses IP pour vos clients approuvés. Une fois activé, uniquement les clients qui ont les adresses IP dans la plage définie peuvent se connecter au magasin.

![Paramètres de pare-feu et accès IP] (./media/data-lake-store-secure-data/firewall-ip-access.png "Paramètres du pare-feu et l’adresse IP")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Supprimer des groupes de sécurité pour un compte Azure données Lake Store

Lorsque vous supprimez des groupes de sécurité à partir de comptes Azure données Lake Store, vous modifiez uniquement l’accès pour les opérations de gestion sur le compte à l’aide du portail Azure et API du Gestionnaire de ressources Azure.

1. Dans votre carte de compte données Lake Store, cliquez sur **paramètres**. À partir de la carte de **paramètres** , cliquez sur **utilisateurs**.

    ![Affecter compte Azure données Lake groupe de sécurité] (./media/data-lake-store-secure-data/adl.select.user.icon.png "Affecter compte Azure données Lake groupe de sécurité")

2. Dans la carte **utilisateurs** cliquez sur le groupe de sécurité que vous voulez supprimer.

    ![Groupe de sécurité à supprimer] (./media/data-lake-store-secure-data/adl.add.user.3.png "Groupe de sécurité à supprimer")

3. Dans la carte du groupe de sécurité, cliquez sur **Supprimer**.

    ![Groupe de sécurité supprimé] (./media/data-lake-store-secure-data/adl.remove.group.png "Groupe de sécurité supprimé")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Supprimer le groupe de sécurité utilisateurs de système de fichiers de magasin de Lake données Azure

Lorsque vous supprimez des groupes de sécurité utilisateurs Azure données Lake Store système de fichiers, vous modifiez l’accès aux données du magasin de données Lake.

1. Dans votre carte de compte données Lake Store, cliquez sur **Explorateur de données**.

    ![Créer les répertoires dans un compte Lake de données] (./media/data-lake-store-secure-data/adl.start.data.explorer.png "Créer les répertoires dans un compte Lake de données")

2. Dans la carte de **L’Explorateur de données** , cliquez sur le fichier ou dossier pour lequel vous souhaitez supprimer l’et et puis, dans votre carte de compte, cliquez sur l’icône **d’accès** . Pour supprimer et d’un fichier, vous devez cliquer sur **accès** à partir de la carte **d’Aperçu du fichier** .

    ![Définir les utilisateurs sur le système de fichiers de données Lake] (./media/data-lake-store-secure-data/adl.acl.1.png "Définir les utilisateurs sur le système de fichiers de données Lake")

3. Dans la carte **d’accès** , à partir de la section **Accès personnalisé** , cliquez sur le groupe de sécurité que vous voulez supprimer. Dans la carte **Personnalisée Access** , cliquez sur **Supprimer** , puis sur **OK**.

    ![Attribuer des autorisations au groupe] (./media/data-lake-store-secure-data/adl.remove.acl.png "Attribuer des autorisations au groupe")


## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble Azure Lake de magasin de données](data-lake-store-overview.md)
- [Copier des données d’objets BLOB Azure stockage au magasin Lake des données](data-lake-store-copy-data-azure-storage-blob.md)
- [Utiliser des données Azure Lake Analytique avec données Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Prise en main du Store Lake de données à l’aide de PowerShell](data-lake-store-get-started-powershell.md)
- [Prise en main données Lake Store à l’aide du Kit de développement .NET](data-lake-store-get-started-net-sdk.md)
- [Journaux de diagnostic d’accès de données Lake Store](data-lake-store-diagnostic-logs.md)
