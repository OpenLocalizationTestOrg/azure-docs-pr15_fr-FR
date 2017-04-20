<properties
   pageTitle="Se connecter à la base de données SQL ou SQL Data Warehouse à l’aide de l’authentification Azure Active Directory | Microsoft Azure"
   description="Découvrez comment vous connecter à la base de données SQL à l’aide de l’authentification Azure Active Directory."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/16/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="connecting-to-sql-database-or-sql-data-warehouse-by-using-azure-active-directory-authentication"></a>Connexion à la base de données SQL ou SQL Data Warehouse à l’aide de l’authentification Azure Active Directory

L’authentification Azure Active Directory est un mécanisme de connexion à la base de données SQL Microsoft Azure et [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) à l’aide des identités dans Azure Active Directory (AD Azure). Avec l’authentification Azure Active Directory, vous pouvez gestion centralisée l’identité des utilisateurs de base de données et autres services Microsoft dans un emplacement central. Gestion des ID Central propose un emplacement unique pour gérer les utilisateurs de base de données et simplifie la gestion des autorisations. Avantages sont les suivants :

- Il propose une alternative à l’authentification SQL Server.
- Permet de bloquer la prolifération des identités utilisateur sur les serveurs de base de données.
- Permet de rotation de mot de passe dans un emplacement unique
- Clients peuvent gérer les autorisations de base de données à l’aide de groupes (DAS) externes.
- Il peut éliminer stocker les mots de passe en activant l’authentification Windows intégrée et autres formes d’authentification pris en charge par Azure Active Directory.
- Utilisation de l’authentification Azure Active Directory contenait des utilisateurs de base de données pour authentifier les identités au niveau de la base de données.
- Azure Active Directory prend en charge l’authentification basée sur un jeton pour les applications qui se connectent à la base de données SQL.
- L’authentification Active Directory Azure prend en charge ADFS (Fédération de domaine) ou authentification native utilisateur/mot de passe pour un local Azure Active Directory sans synchronisation du domaine.  
- Azure Active Directory prend en charge les connexions à partir de SQL Server Management Studio qui utilisent universel l’authentification Active Directory, qui inclut l’authentification multifacteur (MFA).  L’authentification Multifacteur inclut une authentification forte avec un éventail d’options de vérification facile — appel téléphonique, message texte, des cartes à puce avec un code confidentiel, ou la notification de l’application mobile. Pour plus d’informations, voir [SSMS prend en charge pour l’authentification Multifacteur d’Azure AD avec la base de données SQL et SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).

Les étapes de configuration incluent les procédures suivantes pour configurer et utiliser l’authentification Azure Active Directory.

1. Créer et remplir un Azure Active Directory.
2. Vérifiez que votre base de données est en V12 de base de données SQL Azure. (Non nécessaire pour SQL Data Warehouse).
3. Facultatif : Associer ou modifier l’annuaire active directory qui est associé à votre abonnement Azure.
4. Créer un administrateur Azure Active Directory pour Azure SQL Server ou [SQL Azure Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
5. Configurer vos ordinateurs client.
6. Créer des utilisateurs de base de données contenues dans votre base de données mappé avec les identités Azure AD.
7. Se connecter à votre base de données à l’aide des identités Azure AD.


## <a name="trust-architecture"></a>Architecture de gestion de la confidentialité

Le diagramme de haut niveau suivant résume l’architecture de solution de l’utilisation de l’authentification Azure Active Directory avec la base de données SQL Azure. Les mêmes concepts s’appliquent à Data Warehouse SQL. Pour prendre en charge de mot de passe utilisateur native Azure Active Directory, uniquement la partie Cloud et base de données SQL Azure AD/Azure est considéré comme. Pour prendre en charge l’authentification fédéré (ou utilisateur et mot de passe pour les informations d’identification Windows), la communication avec ADFS bloc est requise. Les flèches indiquent voies de communication.

![diagramme d’auth AAD][1]

Le diagramme suivant indique la fédération, gestion de la confidentialité et les relations d’hébergement qui autorise un client à se connecter à une base de données en envoyant un jeton. Le jeton est authentifié par une annonce Azure et est approuvé par la base de données. Client 1 peut représenter Azure Active Directory avec des utilisateurs natives ou Azure Active Directory avec les utilisateurs fédérés. Client 2 représente une solution possible, y compris les utilisateurs importés ; Dans cet exemple provenant d’un fédérés Azure Active Directory avec les services ADFS en cours de synchronisation avec Azure Active Directory. Il est important de comprendre que l’accès à une base de données à l’aide de l’authentification Azure Active Directory requiert que l’abonnement d’hébergement est associé à Azure Active Directory. Le même abonnement doit être utilisé pour créer le SQL Server hébergeant la base de données SQL Azure ou SQL Data Warehouse.

![relation de l’abonnement][2]

## <a name="administrator-structure"></a>Structure de l’administrateur

Lorsque vous utilisez l’authentification Azure Active Directory, il existe deux comptes d’administrateur pour le serveur de base de données SQL ; l’administrateur de SQL Server d’origine et l’administrateur Azure AD. Les mêmes concepts s’appliquent à Data Warehouse SQL. Seul l’administrateur basé sur un compte Azure AD peut créer le premier utilisateur de base de données Azure AD contenue dans une base de données utilisateur. Connexion de l’administrateur Azure AD peut être un utilisateur Azure AD ou un groupe d’annonces Azure. Lorsque l’administrateur est un compte de groupe, il peut être utilisée par n’importe quel membre du groupe, l’activation de plusieurs administrateurs d’Azure AD pour l’instance SQL Server. À l’aide du compte de groupe en tant qu’administrateur améliore la facilité de gestion en vous permettant de manière centralisée ajouter et supprimer des membres du groupe dans Azure AD sans modifier les utilisateurs ou les autorisations de base de données SQL. Seul un administrateur Azure AD (un utilisateur ou groupe) peut être configuré à tout moment.

![structure d’administration][3]

## <a name="permissions"></a>Autorisations

Pour créer de nouveaux utilisateurs, vous devez le `ALTER ANY USER` autorisation dans la base de données. La `ALTER ANY USER` autorisation peut être accordée à tout utilisateur de base de données. La `ALTER ANY USER` autorisation est également occupée par les comptes d’administrateur de serveur et les utilisateurs de base de données avec le `CONTROL ON DATABASE` ou `ALTER ON DATABASE` autorisation pour cette base de données et par les membres de le `db_owner` rôle de base de données.

Pour créer un utilisateur de base de données contenues dans la base de données SQL Azure ou SQL Data Warehouse, vous devez vous connecter à la base de données à l’aide d’une identité Azure AD. Pour créer le premier utilisateur de base de données qu’il contient, vous devez vous connecter à la base de données à l’aide d’un administrateur Azure Active Directory (qui est le propriétaire de la base de données). Ceci est illustré dans les étapes 4 et 5 ci-dessous. N’importe quelle authentification Azure Active Directory est uniquement possible si l’administrateur Azure Active Directory a été créé pour la base de données SQL Azure ou Data Warehouse SQL server. Si l’administrateur Azure Active Directory a été supprimé à partir du serveur, les utilisateurs existants Azure Active Directory créés précédemment à l’intérieur de SQL Server peuvent n’est plus se connecter à la base de données à l’aide de leurs informations d’identification Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Limitations et azure AD fonctionnalités

Les membres d’Azure Active Directory suivants peuvent être mis en service dans Azure SQL Server ou SQL Data Warehouse :

- Membres natives : un membre créé dans Azure Active Directory dans le domaine géré ou dans un domaine de client. Pour plus d’informations, voir [Ajouter votre propre nom de domaine à Azure Active Directory](../active-directory/active-directory-add-domain.md).

- Fédérés membres du domaine : un membre créé dans Azure Active Directory avec un domaine fédéré. Pour plus d’informations, voir [Microsoft Azure maintenant prend en charge la fédération avec Active Directory Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).

- Membres importés à partir d’autres annuaires Active Azure qui sont membres d’un domaine fédéré ou natif.

- Groupes Active Directory créés sous forme de groupes de sécurité.

Comptes Microsoft (outlook.com, hotmail.com, live.com, par exemple) ou autres comptes invité (par exemple gmail.com, yahoo.com) ne sont pas pris en charge. Si vous pouvez vous connecter à [https://login.live.com](https://login.live.com) utilisant le compte et le mot de passe, vous utilisez un compte Microsoft, qui n’est pas prise en charge pour l’authentification Azure AD sur base de données SQL Azure ou SQL Azure Data Warehouse.

### <a name="additional-considerations"></a>Considérations supplémentaires

- Pour améliorer la facilité de gestion, il est recommandé de que vous devez configurer un groupe Azure Active Directory dédié en tant qu’administrateur.
- Seul un administrateur Azure AD (un utilisateur ou groupe) peut être configuré pour une Azure SQL Server ou SQL Azure Data Warehouse à tout moment.
- Seul un administrateur Azure Active Directory pour SQL Server peut se connecter initialement au serveur SQL Azure ou Azure SQL Data Warehouse à l’aide d’un compte Azure Active Directory. L’administrateur Active Directory peut configurer les utilisateurs de base de données Azure Active Directory suivants.
- Nous vous recommandons de définir le délai de connexion à 30 secondes.
- SQL Server 2016 Management Studio et SQL Server Data Tools pour Visual Studio 2015 (version 14.0.60311.1April 2016 ou version ultérieure) prend en charge l’authentification Azure Active Directory. (L’authentification azure Active Directory est prise en charge par le **fournisseur de données .NET Framework pour SQL Server**; à la version minimale .NET Framework 4.6). Par conséquent la dernière version de ces outils et les applications de couche données (DAC et .bacpac) peut utiliser l’authentification Azure Active Directory.
- [ODBC version 13.1](https://www.microsoft.com/download/details.aspx?id=53339) prend en charge l’authentification Azure Active Directory toutefois `bcp.exe` Impossible de se connecter à l’aide de l’authentification Azure Active Directory, car elle utilise un fournisseur ODBC plus ancien.
- `sqlcmd`prend en charge le début de l’authentification Azure Active Directory avec la version 13.1 disponible à partir du [Centre de téléchargement](http://go.microsoft.com/fwlink/?LinkID=825643).  
- SQL Server Data Tools pour Visual Studio 2015 nécessite au minimum la version 2016 d’avril des outils de données (version 14.0.60311.1). Actuellement les utilisateurs Azure Active Directory ne sont pas affichés dans l’Explorateur d’objets SSDT. Pour résoudre ce problème, afficher les utilisateurs de [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
- [Microsoft JDBC pilote 6.0 pour SQL Server](https://www.microsoft.com/en-us/download/details.aspx?id=11774) prend en charge l’authentification Azure Active Directory. En outre, voir [définir les propriétés de connexion](https://msdn.microsoft.com/library/ms378988.aspx).
- PolyBase ne peut pas s’authentifier à l’aide de l’authentification Azure Active Directory.
- Certains outils tels que BI et Excel ne sont pas pris en charge.
- L’authentification Azure Active Directory est pris en charge pour la base de données SQL par les cartes de **Bases de données importer** et **Exporter** du portail Azure. Importer et exporter à l’aide de l’authentification Azure Active Directory est également prise en charge de la commande PowerShell.


## <a name="1-create-and-populate-an-azure-ad"></a>1. créer et remplir une annonce Azure

Créer un Azure Active Directory et y ajouter des utilisateurs et groupes. Azure Active Directory peut être le domaine géré domaine initial AD Azure. Azure Active Directory peut également être un local Active Directory Domain Services fédérée avec Azure Active Directory.

Pour plus d’informations, voir [intégration de vos identités locales avec Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Ajouter votre propre nom de domaine à Azure Active Directory](../active-directory/active-directory-add-domain.md), [Microsoft Azure maintenant prend en charge la fédération avec Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [administrer votre annuaire Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx)et [Gérer Azure AD à l’aide de Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

## <a name="2-ensure-your-sql-database-is-version-12"></a>2. Assurez-vous que votre base de données SQL est version 12

L’authentification Azure Active Directory est prise en charge dans la dernière V12 de base de données SQL. Pour plus d’informations sur V12 de base de données SQL et pour savoir s’il est disponible dans votre région, voir [Nouveautés de la dernière mise à jour V12 SQL de base de données](sql-database-v12-whats-new.md). Cette étape n’est pas nécessaire pour Azure SQL Data Warehouse car SQL Data Warehouse est disponible uniquement dans V12.

Si vous avez une base de données existante, vérifiez qu’il est hébergé dans SQL de base de données V12 en vous connectant à la base de données (par exemple en utilisant SQL Server Management Studio) et l’exécution de `SELECT @@VERSION;`. Le résultat attendu pour une base de données SQL de base de données V12 est au moins **Microsoft SQL Azure (RTM) - 12.0**. Si votre base de données n’est pas hébergée dans V12 de base de données SQL, consultez [planifier et préparer la mise à niveau vers V12 de base de données SQL](sql-database-v12-plan-prepare-upgrade.md), puis rendez-vous sur le portail classique Azure pour migrer la base de données vers SQL de base de données V12.

Par ailleurs, vous pouvez créer une nouvelle base de données dans V12 de base de données SQL en suivant la procédure décrite dans [créer votre première base de données SQL Azure](sql-database-get-started.md). **Conseil**: Lisez l’étape suivante avant de sélectionner un abonnement pour votre nouvelle base de données.

## <a name="3-optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>3. facultatif : Associer ou modifier l’annuaire active directory qui est associé à votre abonnement Azure

Pour associer votre base de données à l’annuaire Azure AD pour votre organisation, sélectionnez le répertoire un approuvé pour l’abonnement Azure qui héberge la base de données. Pour plus d’informations, voir [comment Azure abonnements sont associés à Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Plus d’informations :** Chaque abonnement Azure a une relation d’approbation avec une instance Azure AD. Cela signifie qu’il reconnaît ce répertoire pour l’authentification des utilisateurs, les services et les appareils mobiles. Plusieurs abonnements fiables le même répertoire, mais un abonnement n'approuve qu’un seul répertoire. Vous pouvez voir le répertoire approuvé par votre abonnement sous l’onglet **paramètres** en [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Cette relation d’approbation ayant un abonnement avec un répertoire est différente de la relation ayant un abonnement avec toutes les autres ressources Azure (sites Web, bases de données et ainsi de suite), qui sont apparentent à ressources enfants d’un abonnement. Si un abonnement expire, l’accès à ces autres ressources associées à l’abonnement arrête également. Mais l’annuaire reste dans Azure, et vous pouvez associer un autre abonnement à ce répertoire et continuer à gérer les utilisateurs de l’annuaire. Pour plus d’informations sur les ressources, voir [Présentation de l’accès ressource dans Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Les procédures suivantes fournissent des instructions étape par étape sur la modification de l’annuaire associé pour un abonnement donné.

1. Se connecter à votre [Portail classique Azure](https://manage.windowsazure.com/) à l’aide d’un administrateur d’abonnement Azure.
2. Dans la bannière gauche, sélectionnez **paramètres**.
3. Vos abonnements s’affichent dans l’écran Paramètres. Si l’abonnement souhaité n’apparaît pas, cliquez sur **abonnements** en haut, déplacer vers le bas de la zone **Filtre par répertoire** et sélectionnez le répertoire qui contient vos abonnements et puis cliquez sur **Appliquer**.

    ![Sélectionnez l’abonnement][4]
4. Dans la zone **paramètres** , cliquez sur votre abonnement, puis cliquez sur **Modifier le répertoire** dans la partie inférieure de la page.

    ![AD-paramètres-portail][5]
5. Dans la zone **Répertoire modifier** , sélectionnez Azure Active Directory associé à votre SQL Server ou SQL Data Warehouse, puis cliquez sur la flèche pour suivant.

    ![modifier-répertoire-sélectionnez][6]
6. Dans la boîte de dialogue **Confirmer** répertoire mappage, vérifiez que «**seront supprimés tous les administrateurs de co-création.**»

    ![Confirmer modifier-répertoire][7]
7. Cliquez sur le contrôle pour recharger le portail.

> [AZURE.NOTE] Lorsque vous modifiez l’annuaire, l’accès à tous les coadministrateurs, Azure AD utilisateurs et groupes et utilisateurs de ressources de secours répertoire sont supprimés et ils n’ont plus accès à cet abonnement ou ses ressources. Uniquement vous, en tant qu’un administrateur de service, pouvez configurer l’accès pour les principaux basé sur le nouveau répertoire. Cette modification peut prendre un certain temps de se propager à toutes les ressources. Si vous modifiez le répertoire, également l’administrateur Azure AD pour base de données SQL et SQL Data Warehouse et interdire l’accès de base de données pour tous les utilisateurs existants Azure AD. L’administrateur Azure AD doit être réinitialiser (comme décrit ci-dessous) et nouveau Azure AD utilisateurs doivent être créés.

## <a name="4-create-an-azure-ad-administrator-for-azure-sql-server"></a>4. créer un administrateur Azure AD pour Azure SQL Server

Chaque Azure SQL Server (qui héberge une base de données SQL ou SQL Data Warehouse) commence par un compte d’administrateur de serveur unique qui est l’administrateur du serveur SQL Azure. Un deuxième administrateur SQL Server doit être créé, c'est-à-dire un compte Azure Active Directory. Cette entité est créée comme un utilisateur de base de données contenues dans la base de données principale. En tant qu’administrateur, les comptes d’administrateur de serveur sont membres du rôle **db_owner** dans chaque base de données utilisateur et entrer chaque base de données utilisateur en tant que l’utilisateur **dbo** . Pour plus d’informations sur les comptes d’administrateur de serveur, voir [Gérer les bases de données et les connexions de base de données SQL Azure](sql-database-manage-logins.md) et la section **connexions et les utilisateurs** des [instructions de sécurité de base de données SQL Azure et Limitations](sql-database-security-guidelines.md).

Lorsque vous utilisez Azure Active Directory avec Geo réplication, l’administrateur Azure Active Directory doit être configuré pour le serveur principal et les serveurs secondaires. Si un serveur ne comporte pas un administrateur Azure Active Directory, puis utilisateurs et connexions Azure Active Directory reçoivent un « Impossible de se connecter » avec le serveur.

> [AZURE.NOTE] Utilisateurs qui ne dépendent pas d’un compte Azure AD (y compris le compte d’administrateur Azure SQL Server), ne peut pas créer les utilisateurs AD Azure, car elles n’ont pas d’autorisation pour valider les utilisateurs de base de données proposée avec Azure Active Directory.

### <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server-by-using-the-azure-portal"></a>Mise en service d’administrateur de votre serveur SQL Azure Azure Active Directory à l’aide du portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), dans le coin supérieur droit, cliquez sur votre connexion à une liste des répertoires Active possible de liste déroulante. Sélectionnez l’annuaire Active Directory correct par défaut Azure AD. Cette étape lie l’association d’abonnement avec Active Directory s’assurer que le même abonnement est utilisé pour les deux Azure SQL Server Azure AD et SQL Server. (Azure SQL Server peut être hébergeant base de données SQL Azure ou SQL Azure Data Warehouse.)

    ![Choisissez ad][8]
2. Dans la bannière gauche Sélectionnez **SQL servers**, sélectionnez votre **SQL server**, puis dans la carte de **SQL Server** , dans la partie supérieure sur **paramètres**.

    ![paramètres d’annonces][9]
3. Dans la carte de **paramètres** , cliquez sur ** Active Directory administrateur.
4. Dans la carte **Active Directory administrateur** , cliquez sur **administrateur Active Directory**et puis, dans la partie supérieure, cliquez sur **ensemble d’administration**.
5. Dans la carte **administrateur d’ajouter** , rechercher un utilisateur, sélectionnez l’utilisateur ou groupe administrateur, puis cliquez sur **Sélectionner**. (La carte administrateur Active Directory affiche tous les membres et les groupes de votre Active Directory. Impossible de sélectionner utilisateurs ou groupes qui sont grisées car ils ne sont pas pris en charge en tant qu’administrateur Azure AD. (Voir la liste des administrateurs pris en charge dans **Azure AD fonctionnalités et Limitations** ci-dessus). Contrôle d’accès basé sur un rôle (RBAC) s’applique uniquement au portail et n’est pas propagé à SQL Server.
6. En haut de la cuillère **administrateur Active Directory** , cliquez sur **Enregistrer**.
    ![Choisissez administrateur][10]

    Le processus de changement de l’administrateur peut prendre plusieurs minutes. Le nouvel administrateur apparaît alors dans la zone **administrateur Active Directory** .

> [AZURE.NOTE] Lorsque vous configurez l’administrateur Azure AD, le nouveau nom d’administrateur (utilisateur ou groupe) ne peut pas déjà exister dans la base de données maître virtuelle en tant qu’un utilisateur de l’authentification SQL Server. Le cas échéant, la configuration d’administration Azure AD échouera ; annulation de sa création et en indiquant que cet un administrateur (nom) déjà existent. Dans la mesure où cet un utilisateur de l’authentification SQL Server ne fait pas partie de l’annonce Azure, n’importe quel effort pour vous connecter au serveur à l’aide de l’authentification Azure Active Directory échoue.

Pour supprimer un administrateur, dans la partie supérieure de la cuillère **administrateur Active Directory** , cliquez sur **Supprimer l’administrateur**, puis cliquez sur **Enregistrer**.

### <a name="provision-an-azure-ad-administrator-for-azure-sql-server-by-using-powershell"></a>Mise en service un administrateur Azure AD pour Azure SQL Server à l’aide de PowerShell

Pour exécuter les applets de commande PowerShell, vous devez avoir Azure PowerShell installé et en cours d’exécution. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

Pour configurer un administrateur Azure AD, exécutez les commandes PowerShell Azure suivantes :

- AzureRmAccount ajouter
- Sélectionnez AzureRmSubscription


Applets de commande utilisées mise en service et la gestion d’administration Azure AD :

| Nom de l’applet de commande                                       | Description                                                                                                     |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [Jeu de AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx)    | Met en service un administrateur Azure Active Directory pour Azure SQL Server ou SQL Azure Data Warehouse. (Doit être de l’abonnement actif.) |
| [Supprimer AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | Supprime un administrateur Azure Active Directory pour Azure SQL Server ou SQL Azure Data Warehouse. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx)    | Renvoie des informations sur un administrateur Azure Active Directory actuellement configurée pour le serveur SQL Azure ou SQL Azure Data Warehouse. |

Utiliser PowerShell commande get-help pour afficher plus de détails pour chacun de ces commandes, par exemple ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Les dispositions de script suivantes un groupe d’administrateurs Azure AD nommé **DBA_Group** (id de l’objet `40b79501-b343-44ed-9ce7-da4c8cc7353f`) pour la **demo_server** server dans un groupe de ressources nommé **groupe 23**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

Le paramètre d’entrée **DisplayName** accepte le nom d’affichage Azure AD ou le nom d’utilisateur Principal. Par exemple, ``DisplayName="John Smith"`` et ``DisplayName="johns@contoso.com"``. Nom d’affichage est pris en charge pour les groupes Azure AD uniquement l’annonce Azure.

> [AZURE.NOTE] La commande PowerShell Azure ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` ne vous empêche pas de mise en service des administrateurs Azure AD pour les utilisateurs non prises en charge. Un utilisateur non prises en charge peut être mis en service, mais ne peut pas se connecter à une base de données. (Voir la liste des administrateurs pris en charge dans **Azure AD fonctionnalités et Limitations** ci-dessus).

L’exemple suivant utilise l' option **ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] Azure AD **ObjectID** est requis lorsque le **nom complet** n’est pas unique. Pour extraire les valeurs **ObjectID** et **nom complet** , utilisez la section Active Directory de portail classique Azure et afficher les propriétés d’un utilisateur ou groupe.

L’exemple suivant renvoie des informations sur l’en cours administrateur Azure AD pour Azure SQL Server :

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

L’exemple suivant supprime un administrateur Azure AD :
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

Vous pouvez également configurer un administrateur Azure Active Directory à l’aide de l’API REST. Pour plus d’informations, voir [référence de l’API REST Service gestion et opérations pour les opérations de bases de données SQL Azure des bases de données SQL Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## <a name="5-configure-your-client-computers"></a>5. configurer vos ordinateurs clients

Sur tous les ordinateurs clients, à partir de laquelle vos applications ou les utilisateurs se connecter à la base de données SQL Azure ou Azure SQL Data Warehouse à l’aide des identités Azure AD, vous devez installer les logiciels suivants :

- .NET framework 4.6 ou version ultérieure à partir de [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Bibliothèque d’authentification Azure Active Directory pour SQL Server (**ADALSQL. DLL**) n’est disponible dans plusieurs langues (x86 et amd64) à partir du centre de téléchargement [Microsoft Active Directory authentification](http://www.microsoft.com/download/details.aspx?id=48742)bibliothèque pour Microsoft SQL Server.

### <a name="tools"></a>Outils

- L’installation de [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ou [SQL Server Data Tools pour Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) répond à la configuration minimale requise .NET Framework 4.6.
- SSMS installe la x86 la version de **ADALSQL. DLL**.
- SSDT installe la version amd64 de **ADALSQL. DLL**.
- La dernière Visual Studio à partir de [Téléchargements de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) possède la configuration requise pour .NET Framework 4.6, mais n’installe pas la version amd64 requise de **ADALSQL. DLL**.

## <a name="6-create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>6. créer des utilisateurs de base de données contenues dans votre base de données mappé avec les identités Azure AD

### <a name="about-contained-database-users"></a>Sur les utilisateurs de base de données qu’il contient

L’authentification Azure Active Directory nécessite que les utilisateurs de base de données doit être créé en tant qu’utilisateurs de base de données qu’il contient. Un utilisateur de base de données contenu basé sur une identité Azure AD, est un utilisateur de base de données qui ne dispose pas d’une connexion dans la base de données principale, et qui correspond à une identité dans le répertoire Azure AD associé à la base de données. L’identité Azure AD peut être un compte d’utilisateur ou un groupe. Pour plus d’informations sur les utilisateurs de base de données qu’il contient, voir [Portable votre base de données de fabrication du contenu aux utilisateurs de base de données](https://msdn.microsoft.com/library/ff929188.aspx).

> [AZURE.NOTE] Utilisateurs de base de données (à l’exception des administrateurs) ne peut pas être créés à l’aide du portail. Rôles RBAC ne sont pas répercutées sur SQL Server, base de données SQL ou Data Warehouse SQL. Rôles RBAC Azure sont utilisées pour gérer les ressources Azure et ne s’appliquent pas aux autorisations de base de données. Par exemple, le rôle de **SQL Server collaborateur** n’accorde pas d’accès pour vous connecter à la base de données SQL ou SQL Data Warehouse. L’autorisation d’accès doit être accordée directement dans la base de données à l’aide des instructions Transact-SQL.

### <a name="connect-to-the-user-database-or-data-warehouse-by-using-sql-server-management-studio-or-sql-server-data-tools"></a>Se connecter à la base de données ou du data warehouse utilisateur à l’aide de SQL Server Management Studio ou SQL Server Data Tools

Pour confirmer l’administrateur Azure AD est correctement configuré, vous connecter à la base de données **principale** à l’aide du compte d’administrateur Azure AD.
Pour configurer un utilisateur de base de données de contenu basée sur les annonces Azure (autres que l’administrateur du serveur qui possède la base de données), vous connecter à la base de données avec une identité Azure AD qui a accès à la base de données.

> [AZURE.IMPORTANT] Prise en charge pour l’authentification Azure Active Directory est disponible avec [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) dans Visual Studio 2015. La version 2016 août de SSMS inclut également prise en charge pour l’authentification Active Directory universel, qui permet aux administrateurs d’exiger l’authentification multifacteur à l’aide d’un appel téléphonique, le message de texte, des cartes à puce avec un code confidentiel, ou la notification de l’application mobile.

#### <a name="connect-using-active-directory-integrated-authentication"></a>Se connecter à l’aide de l’authentification intégrée Active Directory

Utilisez cette méthode si vous êtes connecté à Windows à l’aide de vos informations d’identification Azure Active Directory à partir d’un domaine fédéré.

1. Commencez Management Studio ou outils de données, puis dans la boîte de dialogue **se connecter au serveur** (ou **se connecter au moteur de base de données**), dans la zone **authentification** , sélectionnez **Authentification intégrée à Active Directory**. Aucun mot de passe est nécessaire ou peut être entré car seront affichera, vos informations d’identification pour la connexion.
    ![Sélectionnez l’authentification intégrée Active Directory][11]

2. Cliquez sur le bouton **Options** , puis dans la page **Propriétés de connexion** , dans la zone **se connecter à la base de données** , tapez le nom de la base de données utilisateur que vous voulez vous connecter.
    ![Sélectionnez le nom de la base de données][13]


#### <a name="connect-using-active-directory-password-authentication"></a>Se connecter à l’aide de l’authentification Active Directory mot de passe

Utilisez cette méthode lorsque vous connecter avec un nom principal Azure AD à l’aide de l’annonce Azure géré domaine. Vous pouvez également l’utiliser pour compte fédéré sans accès au domaine, par exemple lorsque vous travaillez à distance.

Utilisez cette méthode si vous êtes connecté à Windows à l’aide des informations d’identification d’un domaine qui n’est pas fédéré avec Azure, ou lorsque vous en utilisant l’authentification Azure Active Directory à l’aide d’Azure AD basé sur initial ou le domaine du client.

1. Démarrez Management Studio ou outils de données et dans la boîte de dialogue **se connecter au serveur** (ou **se connecter au moteur de base de données**), dans la zone **authentification** , sélectionnez **L’authentification Active Directory mot de passe**.
2. Dans la zone **nom d’utilisateur** , tapez votre nom d’utilisateur Azure Active Directory au format **username@domain.com**. Cela doit être un compte à partir d’Azure Active Directory ou un compte à partir d’un domaine fédérer avec Azure Active Directory.
3. Dans la zone **mot de passe** , tapez votre mot de passe utilisateur pour le compte Azure Active Directory ou fédérés compte de domaine.
    ![Sélectionnez l’authentification de mot de passe AD][12]

4. Cliquez sur le bouton **Options** , puis dans la page **Propriétés de connexion** , dans la zone **se connecter à la base de données** , tapez le nom de la base de données utilisateur que vous voulez vous connecter. (Voir le graphique dans l’option précédente).


### <a name="create-an-azure-ad-contained-database-user-in-a-user-database"></a>Créer un utilisateur de base de données Azure AD contenue dans une base de données utilisateur

Pour créer un utilisateur de base de données Azure contenus basée sur les annonces (autres que l’administrateur du serveur qui possède la base de données), connectez-vous à la base de données avec une identité Azure AD, un utilisateur avec au moins l’autorisation **ALTER ANY USER** . Puis utilisez la syntaxe Transact-SQL suivante :

    CREATE USER <Azure_AD_principal_name>
    FROM EXTERNAL PROVIDER;


*Azure_AD_principal_name* peut être le nom d’utilisateur principal d’un utilisateur Azure AD ou le nom d’affichage pour un groupe d’annonces Azure.

**Exemples :** Pour créer une base de données contenue utilisateur représentant une publicité Azure fédérés ou géré utilisateur de domaine :

    CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
    CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Pour créer un utilisateur de base de données contenues représentant une publicité Azure ou fédérés groupe de domaine, entrez le nom d’affichage d’un groupe de sécurité :

    CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;

Pour créer un utilisateur de base de données contenues représentant une application qui se connecte à l’aide d’un jeton Azure AD :

    CREATE USER [appName] FROM EXTERNAL PROVIDER;

Pour plus d’informations sur la création d’utilisateurs de base de données contenues en fonction des identités Azure Active Directory, voir [Créer un utilisateur (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).


> [AZURE.NOTE] Suppression de l’administrateur Azure Active Directory pour Azure SQL Server empêche tout utilisateur de l’authentification Azure AD de se connecter au serveur. Si nécessaire, inutilisable utilisateurs Azure AD peuvent être déplacés manuellement par un administrateur de base de données SQL.

Lorsque vous créez un utilisateur de base de données, cet utilisateur reçoit l’autorisation de **connexion** et peut se connecter à cette base de données en tant que membre du rôle **PUBLIC** . Au départ les seules autorisations disponibles pour l’utilisateur sont toutes les autorisations accordées au rôle **PUBLIC** ou toutes les autorisations accordées aux groupes Windows qu’ils sont membre. Une fois que vous devez configurer un utilisateur de base de données contenus basée sur les AD Azure, vous pouvez accorder à l’utilisateur des autorisations supplémentaires, la même façon que vous accordez une autorisation à un autre type d’utilisateur. En règle générale accorder des autorisations aux rôles de base de données et ajouter des utilisateurs aux rôles. Pour plus d’informations, voir [Concepts de base d’autorisation de moteur de base de données](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Pour plus d’informations sur les rôles de base de données SQL spéciaux, voir [Gérer les bases de données et les connexions de base de données SQL Azure](sql-database-manage-logins.md).
Un domaine fédéré importées dans un domaine gérer, vous devez utiliser l’identité de domaine géré.

> [AZURE.NOTE] Utilisateurs AD Azure sont signalés dans les métadonnées de base de données par type E (EXTERNAL_USER) et pour les groupes avec type X (EXTERNAL_GROUPS). Pour plus d’informations, voir [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).


## <a name="7-connect-by-using-azure-ad-identities"></a>7. vous connecter à l’aide des identités Azure AD

L’authentification Active Directory Azure prend en charge les méthodes suivantes de la connexion à une base de données à l’aide des identités Azure AD :

- À l’aide de l’authentification Windows intégrée
- À l’aide d’un UPN Azure AD et un mot de passe
- À l’aide de l’authentification des jetons Application

### <a name="71-connecting-using-integrated-windows-authentication"></a>7.1. Connexion à l’aide de l’authentification intégrée (Windows)

Pour utiliser l’authentification Windows intégrée, Active Directory de votre domaine doit être fédéré avec Azure Active Directory. Votre application cliente (ou un service de connexion à la base de données) doit être en cours d’exécution sur un ordinateur à un domaine, sous informations d’identification de domaine d’un utilisateur.

Pour vous connecter à une base de données à l’aide de l’authentification intégrée et une identité Azure AD, le mot clé d’authentification dans la chaîne de connexion de base de données doit être défini sur intégrée à Active Directory. L’exemple de code c# suivant utilise ADO .NET.

    string ConnectionString =
    @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Notez que la chaîne de connexion mot clé ``Integrated Security=True`` n’est pas prise en charge pour la connexion à la base de données SQL Azure.
Notez que lors d’une connexion ODBC vous devez supprimer les espaces et définir l’authentification à « ActiveDirectoryIntegrated ».

### <a name="72-connecting-with-an-azure-ad-principal-name-and-a-password"></a>7.2. Connexion avec un nom principal Azure AD et un mot de passe
Pour vous connecter à une base de données à l’aide de l’authentification intégrée et une identité Azure AD, le mot clé d’authentification doit être défini par mot de passe Active Directory. La chaîne de connexion doit contenir ID d’utilisateur/UID et mots clés de mot de passe/PWD et les valeurs. L’exemple de code c# suivant utilise ADO .NET.

    string ConnectionString =
      @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

En savoir plus sur les méthodes d’authentification Azure AD en utilisant les exemples de code démo disponibles à [Azure AD authentification GitHub démo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).


### <a name="73-connecting-with-an-azure-ad-token"></a>7.3 connexion avec un jeton Azure AD
Cette méthode d’authentification permet aux services intermédiaire pour vous connecter à la base de données SQL Azure ou SQL Azure Data Warehouse en obtenant un jeton à partir d’Azure Active Directory (DAS). Il permet des scénarios sophistiquées, y compris l’authentification basée sur le certificat. Vous devez effectuer quatre étapes pour utiliser l’authentification jeton Azure AD :

1. Enregistrer votre application avec Azure Active Directory et obtenir l’id client pour votre code. 
2. Créer un utilisateur de base de données qui représente l’application. (Effectuée à l’étape 6).
3. Créer un certificat sur l’ordinateur client exécute l’application.
4. Ajouter le certificat comme clé pour votre application.

Exemple de chaîne de connexion :

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Pour plus d’informations, consultez le [Blog de sécurité de SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/).

### <a name="connecting-with-sqlcmd"></a>Connexion avec sqlcmd  
Les instructions suivantes, vous connecter à l’aide de version 13.1 de sqlcmd, qui est disponible à partir du [Centre de téléchargement](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="see-also"></a>Voir aussi

[Gestion des bases de données et les connexions de base de données SQL Azure](sql-database-manage-logins.md)

[Utilisateurs de base de données qu’il contient](https://msdn.microsoft.com/library/ff929071.aspx)

[CRÉER des utilisateurs (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)


<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

