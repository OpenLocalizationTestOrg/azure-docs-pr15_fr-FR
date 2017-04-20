<properties
   pageTitle="Créer des solutions intégrées avec SQL Data Warehouse | Microsoft Azure"
   description="Outils et partenaires avec les solutions qui s’intègrent à Data Warehouse SQL. "
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="leverage-other-services-with-sql-data-warehouse"></a>Exploiter les autres services avec SQL Data Warehouse
Outre les fonctionnalités principales, SQL Data Warehouse permet aux utilisateurs de tirer parti de la plupart des autres services dans Azure à côté.  Plus précisément, nous avons actuellement pris des mesures pour intégrer en profondeur avec les éléments suivants :

+ Power BI
+ Données Azure usine
+ Apprentissage automatique Azure
+ Flux de données Azure Analytique

Nous travaillons pour vous connecter avec plusieurs services sur le réseau Azure.

##<a name="power-bi"></a>Power BI
Power BI intégration vous permet de tirer parti de la puissance cluster SQL Data Warehouse avec la création de rapports dynamiques et visualisation de Power BI. L’intégration de Power BI inclut actuellement :

+ **Connexion directe**: plus avancée connexion avec menu déroulant logique sur SQL Data Warehouse.  Vous bénéficiez accélère l’analyse sur une plus grande échelle.
+ **Ouvrir dans Power BI**: le bouton « Ouvrir dans Power BI » passe les informations d’instance à Power BI, ce qui permet une connexion plus transparente.

Voir [intégrer avec Power BI](./sql-data-warehouse-integrate-power-bi.md) ou la [documentation de Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx) pour plus d’informations.

##<a name="azure-data-factory"></a>Données Azure usine
Azure Data Factory permet aux utilisateurs une plateforme managée pour créer des pipelines extraire charge complexes.  Intégration de SQL Data Warehouse avec Azure Data Factory inclut les éléments suivants :

+ **Procédures stockées**: organiser l’exécution des procédures stockées sur Data Warehouse SQL.
+ **Copie**: utilisation chargeur pour déplacer des données dans SQL Data Warehouse.  Cette opération peut utiliser mécanisme de déplacement des données standard de chargeur ou PolyBase en coulisses. 

Voir [intégrer avec Azure Data Factory](./sql-data-warehouse-integrate-azure-data-factory.md) ou la [documentation Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) pour plus d’informations.

##<a name="azure-machine-learning"></a>Apprentissage automatique Azure
Azure apprentissage automatique est un service entièrement géré analytique qui permet aux utilisateurs de créer des modèles complexes exploiter les un grand ensemble d’outils prédictives.  Data Warehouse SQL est pris en charge comme une source et la destination pour ces modèles avec les fonctionnalités suivantes :

+ **Lire les données :** Lecteur modèles à l’échelle à l’aide de T-SQL sur SQL Data Warehouse.
+ **Écrire des données :** Valider des modifications à partir de n’importe quel modèle en Data Warehouse SQL.

Voir [intégrer avec apprentissage automatique Azure](./sql-data-warehouse-integrate-azure-machine-learning.md) ou la [documentation d’apprentissage automatique Azure](https://azure.microsoft.com/services/machine-learning/) pour plus d’informations.

##<a name="azure-stream-analytics"></a>Flux de données Azure Analytique
Azure flux Analytique est une infrastructure complexe, entièrement gérée pour traitement et l’utilisation des données d’événement générées à partir d’Azure événement concentrateur.  Intégration avec SQL Data Warehouse permet de diffusion en continu de données pour être efficacement traitées et stockées à côté des données relationnelles permettant l’analyse plus approfondie, plus avancée.  

+ **Sortie de tâche :** Envoyer la sortie de tâches de flux de données Analytique directement vers SQL Data Warehouse.

Voir [intégrer avec Azure flux Analytique](./sql-data-warehouse-integrate-azure-stream-analytics.md) ou la [documentation Azure flux Analytique](https://azure.microsoft.com/documentation/services/stream-analytics/) pour plus d’informations.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->
