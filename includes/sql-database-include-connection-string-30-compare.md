
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>Comparer la chaîne de connexion


Le tableau suivant compare les chaînes de connexion votre programme c# doit se connecter à votre serveur SQL local par rapport à votre base de données SQL Azure dans le cloud. Les différences apparaissent en gras.


| Chaîne de connexion pour<br/>Base de données SQL Azure | Chaîne de connexion pour<br/>Microsoft SQL Server |
| :-- | :-- |
| Serveur =**tcp :**{your_serverName_here}**. database.windows.net,1433**;<br/>ID d’utilisateur = {your_loginName_here}**@{your_serverName_here}**;<br/>Mot de passe = {your_password_here} ;<br/>**Base de données = {your_databaseName_here} ;**<br/>**Délai de connexion = 30**;<br/>**Chiffrer = True**;<br/>**TrustServerCertificate = False**; | Serveur = {your_serverName_here} ;<br/>ID d’utilisateur = {your_loginName_here} ;<br/>Mot de passe = {your_password_here} ; |


La **base de données =** est facultative pour SQL Server, mais est requis pour la base de données SQL.


[Propriétés de SqlConnectionStringBuilder ADO .NET](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) - traite de tous les paramètres dans le détail.


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
