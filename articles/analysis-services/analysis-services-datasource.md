<properties
   pageTitle="Connexions de source de données | Microsoft Azure"
   description="Décrit les connexions de source de données pour les modèles de données dans Azure Analysis Services."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="owend"/>

# <a name="datasource-connections"></a>Connexions de source de données

Modèles de données dans Azure Analysis Services peuvent nécessiter des fournisseurs de données différents lors de la connexion pour certaines sources de données. Dans certains cas, les modèles tabulaires connexion aux sources de données à l’aide de fournisseurs natifs tels que SQL Server Native Client (SQLNCLI11) peuvent renvoyer une erreur.

Par exemple ; Si vous avez en mémoire ou requête directe du modèle de données qui se connecte à une source de données cloud comme base de données SQL Azure, si vous utilisez des fournisseurs natifs différent SQLOLEDB, vous pouvez voir le message d’erreur : **« Le fournisseur 'SQLNCLI11.1' n’est pas enregistré »**.

Ou, si vous avez un modèle de DirectQuery connexion aux sources de données locales, si vous utilisez des fournisseurs natifs que vous voyiez message d’erreur : **« erreur de création du jeu de lignes OLE DB. Syntaxe incorrecte près de « Limite » «**.

## <a name="data-source-providers"></a>Fournisseurs de sources de données

Les fournisseurs de source de données suivants sont pris en charge pour en mémoire ou diriger les modèles de données de requête lors de la connexion vers local ou sur le cloud sources de données :

|               | **Source de données**                     | **En mémoire**                            |  **Requête directe**                                           |
|---------------------------|-------------------------------|---------------------------------------------|---------------------------------------------|
| **Cloud**                     | Data Warehouse SQL Azure      | Fournisseur de données .NET framework pour SQL Server | Fournisseur de données .NET framework pour SQL Server |
|                           | Base de données SQL Azure            | Fournisseur de données .NET framework pour SQL Server | Fournisseur de données .NET framework pour SQL Server |
| **En local** (via une passerelle) | SQL Server                    | SQL Server Native Client 11.0               | Fournisseur de données .NET framework pour SQL Server |
|                           |  SQL Server                             | Fournisseur Microsoft OLE DB pour SQL Server    |   Fournisseur de données .NET framework pour SQL Server                                          |
|                           |  SQL Server                             | Fournisseur de données .NET framework pour SQL Server |  Fournisseur de données .NET framework pour SQL Server                                           |
|                           | Oracle                        | Fournisseur Microsoft OLE DB pour Oracle        | Fournisseur de données Oracle pour .NET               |
|                           |  Oracle                             | Fournisseur de données Oracle pour .NET               | Fournisseur de données Oracle pour .NET                                            |
|                           | Teradata                      | Fournisseur OLE DB pour Teradata                | Fournisseur de données Teradata pour .NET             |
|                           |  Teradata                             | Fournisseur de données Teradata pour .NET             |  Fournisseur de données Teradata pour .NET                                            |
|                           | Système de plateforme Analytique | Fournisseur de données .NET framework pour SQL Server | Fournisseur de données .NET framework pour SQL Server |


> [AZURE.NOTE] Vérifiez les fournisseurs de 64 bits sont installés lors de l’utilisation de passerelle en local.

Lorsque vous migrez un modèle tabulaire de SQL Server Analysis Services local à Azure Analysis Services, il peut être nécessaire remplacer le fournisseur.

**Pour spécifier un fournisseur de source de données**

1. Dans SSDT > **Explorateur de modèle tabulaire** > de**Sources de données**, avec le bouton droit à une connexion de source de données, puis cliquez sur **Modifier la Source de données**.

2. Dans **Modifier la connexion**, cliquez sur **Avancé** pour ouvrir la fenêtre de propriétés avancées.

3. Dans **Définir les propriétés avancées** > **fournisseurs**, puis sélectionnez le fournisseur approprié.

## <a name="impersonation"></a>Emprunt d’identité
Dans certains cas, il peut être nécessaire spécifier un compte d’emprunt d’identité autre. Compte d’emprunt d’identité peut être indiquée dans SSDT ou SSMS.

Pour les sources de données locales :

- Si vous utilisez l’authentification SQL, l’emprunt d’identité doit être compte de Service.
- Si vous utilisez l’authentification Windows, définissez Windows utilisateur et mot de passe. Pour SQL Server, l’authentification Windows avec un compte d’emprunt d’identité spécifique est prise en charge uniquement pour les modèles de données en mémoire.

Pour les sources de données cloud :

- Si vous utilisez l’authentification SQL, l’emprunt d’identité doit être compte de Service.


## <a name="next-steps"></a>Étapes suivantes

Si vous avez des sources de données locales, veillez à installer la [passerelle locale](analysis-services-gateway.md). Pour plus d’informations sur la gestion de votre serveur dans SSDT ou SSMS, voir [Gérer votre serveur](analysis-services-manage.md).
