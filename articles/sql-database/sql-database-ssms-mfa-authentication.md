<properties
   pageTitle="SSMS prend en charge pour l’authentification Multifacteur d’Azure AD avec la base de données SQL et SQL Data Warehouse | Microsoft Azure"
   description="Utiliser l’authentification Multi-prises en charge avec SSMS pour base de données SQL et SQL Data Warehouse."
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
   ms.date="10/04/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>Prise en charge SSMS pour l’authentification Multifacteur d’Azure AD avec la base de données SQL et SQL Data Warehouse

Base de données SQL Azure et de magasin de données SQL Azure désormais en charge les connexions à partir de SQL Server Management Studio (SSMS) à l’aide de *L’authentification universel Active Directory*. Authentification universel Active Directory est un flux de travail interactive qui prend en charge *L’authentification multifacteur Azure* (MFA). Azure l’authentification Multifacteur permet divulgation d’informations l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple. Il propose une authentification forte avec un éventail d’options de vérification facile — appel téléphonique, message texte, des cartes à puce avec un code confidentiel, ou la notification de l’application mobile, ce qui permet aux utilisateurs de choisir la méthode préférez. Pour obtenir une description de l’authentification multifacteur, voir [L’authentification multifacteur](../multi-factor-authentication/multi-factor-authentication.md).

SSMS prend désormais en charge :

- Authentification Multifacteur interactif avec Azure AD avec la possibilité de validation de boîte de dialogue qui s’affiche.
- Méthodes de mot de passe Active Directory et l’authentification intégrée Active Directory non interactif peuvent être utilisés dans différentes applications (ADO.NET, JDBC, ODBC, etc.). Ces deux méthodes résultent jamais dans les boîtes de dialogue qui s’affiche.

Lorsque le compte d’utilisateur est configuré pour l’authentification Multifacteur du flux de travail de l’authentification interactive nécessite une interaction utilisateur supplémentaires dans les boîtes de dialogue qui s’affiche, utiliser des cartes à puce, etc.. Lorsque le compte d’utilisateur est configuré pour l’authentification Multifacteur, l’utilisateur doit sélectionner authentification universel Azure pour vous connecter. Si le compte d’utilisateur ne requiert pas l’authentification Multifacteur, l’utilisateur peut toujours utiliser les deux autres options de l’authentification Azure Active Directory.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limites de l’authentification universels pour la base de données SQL et SQL Data Warehouse

- SSMS est le seul outil actuellement activé pour l’authentification Multifacteur via l’authentification universel Active Directory.
- Un seul compte Azure Active Directory peut se connecter pour une instance de SSMS en utilisant l’authentification universel. Pour vous connecter sous un autre compte Azure AD, vous devez utiliser une autre instance de SSMS. (Cette restriction est limitée à l’authentification Active Directory universel ; vous pouvez vous connecter à différents serveurs à l’aide de l’authentification Active Directory mot de passe, l’authentification intégrée Active Directory ou l’authentification SQL Server).
- SSMS prend en charge l’authentification universel Active Directory pour visualisation Explorateur d’objets, l’éditeur de requête et la requête sécurisée.
- Le Concepteur de schéma ni DacFx prend en charge l’authentification universel.
- Comptes MSA ne sont pas prises en charge pour l’authentification universel Active Directory.
- Authentification universel Active Directory n’est pas pris en charge dans SSMS pour les utilisateurs qui sont importées dans Active Directory à partir d’autres annuaires Active Azure active. Ces utilisateurs ne sont pas pris en charge, car il nécessite un ID de client valider les comptes, et il n’existe aucun mécanisme de prévoir que.
- Il n’existe aucune configuration logicielle supplémentaire requise pour l’authentification Active Directory universel sauf que vous devez utiliser une version prise en charge de SSMS.

## <a name="configuration-steps"></a>Étapes de configuration

Implémentation de l’authentification multifacteur nécessite quatre étapes.

1. **Configurer un Azure Active Directory** – pour plus d’informations, voir [intégration de vos identités locales avec Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Ajouter votre propre nom de domaine à Azure Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure maintenant prend en charge la fédération avec Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [administrer votre annuaire Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx)et [Gérer Azure AD à l’aide de Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

2. **Configurer l’authentification Multifacteur** – pour obtenir des instructions détaillées, voir [Configuration de l’authentification multifacteur Azure](../multi-factor-authentication/multi-factor-authentication-whats-next.md). 

3. **Configurer la base de données SQL ou SQL Data Warehouse pour l’authentification Active Directory Azure** – pour obtenir des instructions détaillées, consultez l’article [connexion à base de données SQL ou données entrepôt par à l’aide de Azure Active Directory l’authentification SQL](sql-database-aad-authentication.md).

4. **Télécharger SSMS** – sur l’ordinateur client, téléchargez la dernière SSMS (au moins 2016 août), de [Télécharger SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Connexion en utilisant l’authentification universel avec SSMS

Les étapes suivantes montrent comment se connecter à la base de données SQL ou SQL Data Warehouse à l’aide de la dernière SSMS.

1. Pour vous connecter à l’aide de l’authentification universel, dans la boîte de dialogue **se connecter au serveur** , activez **L’authentification universel Active Directory**.
![se connecter 1mfa-universel][1]

2. Comme d’habitude pour la base de données SQL et SQL Data Warehouse, vous devez cliquez sur **Options** et spécifier la base de données dans la boîte de dialogue **Options** . Cliquez ensuite sur **se connecter**.
3. Lorsque la boîte de dialogue **se connecter à votre compte** s’affiche, indiquez le compte et le mot de passe de votre identité Azure Active Directory.
![2mfa-connexion][2]

    > [AZURE.NOTE] Pour l’authentification universel avec un compte qui ne nécessite pas l’authentification Multifacteur, vous vous connectez à ce stade. Pour des utilisateurs nécessitant l’authentification Multifacteur, passez aux étapes suivantes.
 
4. Deux boîtes de dialogue de configuration de l’authentification Multifacteur peuvent s’afficher. Cette fois opération dépend de l’administrateur de l’authentification Multifacteur définition et par conséquent peut être facultative. Pour un domaine de l’authentification Multifacteur activé cette étape est parfois prédéfinie (par exemple, le domaine nécessite aux utilisateurs d’utiliser une carte à puce et le code confidentiel).  
![programme d’installation 3mfa][3]

5. La deuxième possible une seule fois, boîte de dialogue vous permet de sélectionner les détails de votre méthode d’authentification. Les options possibles sont configurées par votre administrateur.
![4mfa-vérifier-1][4]
 
6. Azure Active Directory vous envoie les informations de confirmation. Lorsque vous recevez le code de vérification, tapez-le dans la zone **code de vérification d’entrée** , puis cliquez sur **se connecter**.
![5mfa-vérifier-2][5]

Lors de la vérification terminée, SSMS connecte supposant en règle générale, les informations d’identification valides et accès au pare-feu.

##<a name="next-steps"></a>Étapes suivantes  

Accorder l’accès à votre base de données : [autorisation et authentification de base de données SQL : autoriser l’accès au](sql-database-manage-logins.md)  
Vérifiez que d’autres personnes peuvent se connecter à travers le pare-feu : [configurer une règle de pare-feu au niveau du serveur de base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md)


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

