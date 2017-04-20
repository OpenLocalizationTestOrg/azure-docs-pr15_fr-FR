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
    ms.date="10/18/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-databases"></a>Bases de données pour le gouvernement Azure

##  <a name="sql-database"></a>Base de données SQL

Reportez-vous à la<a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx"> Centre de sécurité Microsoft pour moteur de base de données SQL</a> et [Documentation Public des base de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/) pour obtenir des conseils supplémentaires sur la configuration de visibilité métadonnées et meilleures pratiques de protection.

### <a name="variations"></a>Variantes

Base de données SQL V12 est disponible à partir de pour le gouvernement Azure.

L’adresse pour SQL Azure serveurs dans Azure pour le gouvernement est différente :

Type de service|Public Azure|Pour le gouvernement Azure
---|---|---
Base de données SQL|*. database.windows.net|*. database.usgovcloudapi.net

### <a name="considerations"></a>Considérations relatives à la

Les informations suivantes identifient la limite pour le gouvernement Azure pour SQL Azure :

| Données réglée/contrôlée autorisé | Données réglée/contrôlée non autorisé |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toutes les données stockées et traitées dans Microsoft Azure SQL peuvent contenir des données réglée pour le gouvernement Azure. Vous devez utiliser les outils de base de données pour le transfert de données de données réglée pour le gouvernement Azure. | Métadonnées SQL Azure ne sont pas autorisée pour contenir contrôlé à l’exportation des données. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre produit de stockage.  Ne saisissez réglée/contrôlé des données dans les champs suivants : nom, nom de l’abonnement, groupes de ressources, nom du serveur, connexion au serveur d’administration, les noms de déploiement, les noms de ressources, les balises de ressources de base de données

## <a name="azure-redis-cache"></a>Cache Redis Azure

Pour plus d’informations sur ce service et comment l’utiliser, voir [documentation publique Azure Redis Cache](https://azure.microsoft.com/documentation/services/redis-cache/).

### <a name="variations"></a>Variantes

Toutes les URL d’accès et de gestion Azure Redis Cache dans Azure pour le gouvernement sont différentes :

Type de service|Public Azure|Pour le gouvernement Azure
---|---|---
Point de terminaison de cache|*. redis.cache.windows.net|*. redis.cache.usgovcloudapi.net
Portail Azure|https://Portal.Azure.com|https://Portal.Azure.us

>[AZURE.NOTE] Tous les scripts et code doit prendre en compte les environnements et les points de terminaison appropriés. Pour plus d’informations, voir [comment vous connecter à Azure pour le gouvernement Cloud](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).


### <a name="considerations"></a>Considérations relatives à la

Les informations suivantes identifient la limite pour le gouvernement Azure Azure Redis cache :

| Données réglée/contrôlée autorisé | Données réglée/contrôlée non autorisé |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toutes les données stockées et traitées dans Azure Redis Cache peuvent contenir des données réglée pour le gouvernement Azure. | Métadonnées Redis Cache Azure ne sont pas autorisée pour contenir contrôlé à l’exportation des données. Ne saisissez réglée/contrôlé des données dans les champs suivants : nom, nom VNET, nom de l’abonnement, groupes de ressources, les balises de ressources, propriétés Redis en Cache.  

##  <a name="next-steps"></a>Étapes suivantes

Pour des informations supplémentaires et des mises à jour s’abonner à la <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure pour le gouvernement.</a>
