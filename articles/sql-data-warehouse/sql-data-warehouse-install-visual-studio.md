<properties
   pageTitle="Installer Visual Studio et SSDT pour SQL Data Warehouse | Microsoft Azure"
   description="Installer Visual Studio et outils de développement SQL Server (SSDT) pour Data Warehouse SQL Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="install-visual-studio-2015-and-ssdt-for-sql-data-warehouse"></a>Installer Visual Studio 2015 et SSDT pour Data Warehouse SQL

Pour développer des applications pour Data Warehouse SQL, nous vous recommandons d’utiliser Visual Studio 2015 avec la version la plus récente de SQL Server Data Tools (SSDT).  5 de mise à jour Visual Studio 2013 avec SSDT est également prise en charge pour la compatibilité descendante.  

À l’aide de Visual Studio avec SSDT vous permettra d’utiliser l’Explorateur d’objets SQL Server visuellement Explorer des tables, des affichages, des procédures stockées et beaucoup plus d’objets dans votre Data Warehouse SQL mais aussi exécuter des requêtes.

> [AZURE.NOTE] SQL Data Warehouse ne prend pas en charge les projets de base de données Visual Studio.  Cette fonctionnalité sera ajoutée dans une version ultérieure.

## <a name="step-1-install-visual-studio-2015"></a>Étape 1 : Installer Visual Studio 2015

Suivez ces liens pour télécharger et installer Visual Studio 2015. Si vous avez déjà Visual Studio 2013 ou 2015 installé, vous pouvez passer à l’étape 2, installer SSDT.

1. [Télécharger Visual Studio 2015][].
2. Suivez le guide de [L’installation de Visual Studio][] sur MSDN et choisissez les configurations par défaut.

## <a name="step-2-install-ssdt"></a>Étape 2 : Installer SSDT

Pour installer SSDT pour Visual Studio rechercher simplement une mise à jour SSDT à partir de Visual Studio en procédant comme suit.

1. Dans Visual Studio, cliquez sur **Outils** / **Extensions et mises à jour...**  /  **Mises à jour**
2. Sélectionnez les **Mises à jour de produit** , puis recherchez des **Outils de mise à jour de Microsoft SQL Server pour la base de données**

Si une mise à jour n’est pas trouvée, vous devez la dernière version installée.  Pour confirmer SSDT est installé, cliquez sur **aide** / **à propos de Microsoft Visual Studio** et apparence pour SQL Server Data Tools dans la liste.  La dernière version de SSDT est 14.0.60525.0.  Si l’option d’installation n’est pas disponible à partir de Visual Studio, vous pouvez également visiter la page [SSDT télécharger][] pour télécharger et installer SSDT manuellement.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez la dernière version de SSDT, vous êtes prêt à [vous connecter][] à votre Data Warehouse SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[se connecter]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Télécharger Visual Studio 2015]: https://www.visualstudio.com/downloads/
[L’installation de Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Téléchargement SSDT]: https://msdn.microsoft.com/library/mt204009.aspx
