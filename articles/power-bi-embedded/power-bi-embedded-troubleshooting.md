<properties
   pageTitle="Résolution des problèmes de Microsoft Power BI Preview incorporé"
   description="Résolution des problèmes de Microsoft Power BI Preview incorporé"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Résolution des problèmes de Microsoft Power BI Preview incorporé
Cet article fournit des réponses pour savoir comment résoudre les problèmes de **Power BI incorporé**.

<a name="connection-string"/>
## <a name="setting-sql-server-connection-strings"></a>Définir les chaînes de connexion SQL Server
Pour définir une chaîne de connexion SQL Server, vous devez suivre un format spécifique. Voici un exemple de chaîne de connexion pour SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Pour en savoir plus sur les chaînes de connexion SQL Server, voir les articles suivants :

-   [Chaînes de connexion SQL Server](https://msdn.microsoft.com/library/jj653752.aspx)
-   [SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## <a name="setting-credentials"></a>Définir les informations d’identification
Dans le cas où vous disposez des informations d’identification pour un développement ou un environnement intermédiaire, telles que le nom d’utilisateur et mot de passe, vous devrez peut-être mettre à jour les informations d’identification qui correspondent à une solution de production.

## <a name="see-also"></a>Voir aussi
- [Prise en main exemple](power-bi-embedded-get-started-sample.md)
- [Qu’est incorporé Power BI](power-bi-embedded-what-is-power-bi-embedded.md)
