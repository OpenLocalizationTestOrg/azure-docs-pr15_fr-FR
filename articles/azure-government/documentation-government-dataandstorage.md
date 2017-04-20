<properties
    pageTitle="Documentation pour le gouvernement Azure | Microsoft Azure"
    description="Cela fournit une comparaison des fonctionnalités et des recommandations sur le développement d’applications pour le gouvernement Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="09/30/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-data-and-storage"></a>Stockage et des données pour le gouvernement azure

##  <a name="azure-storage"></a>Stockage Azure

Pour plus d’informations sur ce service et comment l’utiliser, voir la [documentation publique du stockage Azure](https://azure.microsoft.com/documentation/services/storage/).

### <a name="variations"></a>Variantes

Les URL des comptes de stockage dans Azure pour le gouvernement sont différentes :

Type de service|Public Azure|Pour le gouvernement Azure
---|---|---
Stockage d’objets BLOB|*. blob.core.windows.net|*. blob.core.usgovcloudapi.net
Stockage de file d’attente|*. queue.core.windows.net|*. queue.core.usgovcloudapi.net
Stockage de table|*. table.core.windows.net| *. table.core.usgovcloudapi.net

>[AZURE.NOTE] Tous les scripts et code doit prendre en compte les points de terminaison appropriés.  Voir [configurer des chaînes de connexion de stockage Azure](../storage-configure-connection-string.md#creating-a-connection-string-to-the-explicit-storage-endpoint). 

Pour plus d’informations sur les API voir le <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">Constructeur de compte de stockage Cloud</a>.

Le suffixe point de terminaison à utiliser dans ces surcharges est core.usgovcloudapi.net 

### <a name="considerations"></a>Considérations relatives à la

Les informations suivantes identifient la limite pour le gouvernement Azure Azure stockage :

| Données réglée/contrôlée autorisé | Données réglée/contrôlée non autorisé |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Données entrées, stockés et traités au sein d’un espace de stockage Azure produit peut contenir des données contrôlé à l’exportation. Authentificateurs statiques, tels que les mots de passe et des codes confidentiels carte à puce pour l’accès aux composants de la plateforme Azure. Clés privées des certificats utilisés pour gérer les composants de la plateforme Azure. Autres sécurité informations/secrets, tels que les certificats, les clés de chiffrement, clés principales et clés de stockage stockées dans les services Azure. | Les métadonnées de stockage Azure ne sont pas autorisée pour contenir contrôlé à l’exportation des données. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre produit de stockage.  Ne saisissez réglementés/contrôlé des données dans les champs suivants : groupes de ressources, les noms de déploiement, les noms de ressources, les balises de ressources  

##  <a name="premium-storage"></a>Stockage Premium

Pour plus d’informations sur ce service et son utilisation, consultez [stockage Premium : stockage High Performance pour charges de travail Azure Machine virtuelle](../storage/storage-premium-storage.md).

###  <a name="variations"></a>Variantes

Stockage Premium est disponible à partir de l’USGov Virginia. Cela inclut les Machines virtuelles série DS. 

### <a name="considerations"></a>Considérations relatives à la

Les mêmes considérations de données de stockage répertoriées ci-dessus appliquent aux comptes de stockage premium. 

##  <a name="sql-database"></a>Base de données SQL

Reportez-vous à la<a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx"> Centre de sécurité Microsoft pour moteur de base de données SQL</a> et [Documentation Public des base de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/) pour obtenir des conseils supplémentaires sur la configuration de visibilité métadonnées et meilleures pratiques de protection.

### <a name="variations"></a>Variantes

Base de données SQL V12 est disponible à partir de pour le gouvernement Azure.

L’adresse pour SQL Azure serveurs dans Azure pour le gouvernement est différente :

Type de service|Public Azure|Pour le gouvernement Azure
---|---|---
Base de données SQL|*. database.windows.net|*. database.usgovcloudapi.net

### <a name="considerations"></a>Considérations relatives à la

Les informations suivantes identifient la limite pour le gouvernement Azure Azure stockage :

| Données réglée/contrôlée autorisé | Données réglée/contrôlée non autorisé |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toutes les données stockées et traitées dans Microsoft Azure SQL peuvent contenir des données réglée pour le gouvernement Azure. Vous devez utiliser les outils de base de données pour le transfert de données de données réglée pour le gouvernement Azure. | Métadonnées SQL Azure ne sont pas autorisée pour contenir contrôlé à l’exportation des données. Ces métadonnées incluent toutes les données de configuration saisies lors de la création et la gestion de votre produit de stockage.  Ne saisissez réglée/contrôlé des données dans les champs suivants : nom, nom de l’abonnement, groupes de ressources, nom du serveur, connexion au serveur d’administration, les noms de déploiement, les noms de ressources, les balises de ressources de base de données

##  <a name="next-steps"></a>Étapes suivantes

Pour des informations supplémentaires et des mises à jour s’abonner à la <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure pour le gouvernement.</a>
