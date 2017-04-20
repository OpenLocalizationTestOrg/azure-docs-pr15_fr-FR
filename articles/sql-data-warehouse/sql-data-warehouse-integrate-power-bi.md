<properties
   pageTitle="Utiliser Power BI avec SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour l’utilisation de Power BI avec Azure SQL Data Warehouse pour développer des solutions."
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

# <a name="use-power-bi-with-sql-data-warehouse"></a>Utiliser Power BI avec SQL Data Warehouse
En tant que la base de données SQL Azure SQL données Warehouse Direct Connect permet utilisateur tirer parti de menu déroulant logique puissante en parallèle avec les fonctions analytiques de Power BI.  Avec directement à vous connecter, les requêtes sont envoyées à votre Data Warehouse SQL Azure en temps réel comme vous explorez les données.  Cela, combiné avec l’échelle de SQL Data Warehouse permet aux utilisateurs de créer des rapports dynamiques en minutes par rapport à to de données.  En outre, l’introduction du bouton Ouvrir dans Power BI permet aux utilisateurs de se connecter directement Power BI à leur SQL Data Warehouse sans recueillir des informations à partir d’autres parties d’Azure.

Lorsque vous utilisez connexion directe Veuillez Remarque :

+ Spécifiez le nom du serveur complet lors de la connexion (voir ci-dessous pour plus d’informations)
+ Vérifiez les règles de pare-feu pour la base de données sont configurées sur « Autoriser l’accès aux services Azure ».
+ Toutes les actions telles que la sélection d’une colonne ou en ajoutant un filtre interrogent directement le data warehouse
+ Les mosaïques sont actualisées environ 15 minutes (actualisation n’a pas besoin et peut être planifiée)
+ Q & A ne sont pas disponible pour les jeux de données connexion directe
+ Modifications de schéma ne sont pas prises automatiquement
+ Toutes les requêtes de se connecter directement expire après 2 minutes

Afin d’améliorer l’expérience de ces restrictions et les notes peuvent changer. Les étapes de connexion sont détaillées ci-dessous.  

## <a name="using-the-open-in-power-bi-button"></a>Utilisation du bouton « Ouvrir dans Power BI »
Le moyen le plus simple pour vous déplacer entre votre Data Warehouse SQL et Power BI est avec le bouton Ouvrir dans Power BI. Ce bouton vous permet en toute transparence commencer la création de nouveaux tableaux de bord dans Power BI.  

1.  Pour commencer à votre instance SQL Data Warehouse dans le portail classique Azure.
2.  Cliquez sur le bouton « Ouvrir dans Power BI ».
3.  Si nous ne parviennent pas à se connecter directement, ou si vous ne disposez pas d’un compte de Power BI, vous avez besoin pour vous connecter.  
4.  Vous sera dirigé vers la page de connexion Data Warehouse SQL, avec les informations de votre SQL Data Warehouse pré-rempli.
5.  Après avoir entré vos informations d’identification vous serez entièrement connecté à votre Data Warehouse SQL.

## <a name="connecting-through-the-power-bi-portal"></a>Connexion à partir du portail de Power BI
Outre l’utilisation du bouton Ouvrir dans Power BI, les utilisateurs peuvent également se connecter à leur SQL Data Warehouse via le portail de Power BI.

1.  Cliquez sur « Données » dans la partie inférieure du volet de navigation.
2.  Sélectionnez « Bases de données ».
3.  Une fois dans la page bases de données, sélectionnez « Entrepôt de données SQL Azure », puis sur « Se connecter ».
4.  Entrez les informations de connexion nécessaires.  Votre nom de serveur et de base de données sont accessibles dans le portail Azure.
5.  Vous sera dirigé revenir à la page principale de Power BI et une fois la connexion établie une nouvelle entrée sous « Jeux de données » s’affiche avec le nom de votre instance.  
6.   Vous pouvez cliquer sur le nouveau dataset pour explorer toutes les tables et les vues dans votre base de données. Sélection d’une colonne vous envoie une requête à la source, de créer dynamiquement votre visuel. Ces éléments visuels peuvent être enregistrés dans un nouveau rapport et épinglées revenir à votre tableau de bord.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
